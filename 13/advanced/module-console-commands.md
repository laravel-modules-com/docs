---
title: Console Commands
order: 2
---

Modules can define their own Artisan commands. Module names should use CamelCase (e.g. `SerialCode`, not `SERIALCODE`).

## Generating a Command

```bash
php artisan module:make-command ImportPostsCommand Blog
```

This creates `Modules/Blog/app/Console/Commands/ImportPostsCommand.php`:

```php
<?php

namespace Modules\Blog\Console\Commands;

use Illuminate\Console\Command;

class ImportPostsCommand extends Command
{
    protected $signature = 'blog:import-posts';
    protected $description = 'Import posts from the external API.';

    public function handle(): int
    {
        $this->info('Importing posts...');

        // Your logic here

        $this->info('Done.');

        return self::SUCCESS;
    }
}
```

## Registering Commands

Commands must be registered in the module's service provider `registerCommands` method to be available via `php artisan`:

```php
// Modules/Blog/app/Providers/BlogServiceProvider.php

protected function registerCommands(): void
{
    $this->commands([
        \Modules\Blog\Console\Commands\ImportPostsCommand::class,
        \Modules\Blog\Console\Commands\PublishScheduledPostsCommand::class,
    ]);
}
```

Once registered, you can run the command:

```bash
php artisan blog:import-posts
```

## Command Signatures

The `$signature` property follows standard Laravel syntax:

```php
// Required argument
protected $signature = 'blog:publish {post}';

// Optional argument with default
protected $signature = 'blog:publish {post?}';

// Option flag
protected $signature = 'blog:import-posts {--dry-run}';

// Option with value
protected $signature = 'blog:import-posts {--limit=100}';

// Combined
protected $signature = 'blog:import-posts
                        {source : The import source (csv|api)}
                        {--limit=100 : Maximum number of posts to import}
                        {--dry-run : Preview without saving}';
```

## Handling User Input

```php
public function handle(): int
{
    $source = $this->argument('source');
    $limit  = (int) $this->option('limit');
    $dryRun = $this->option('dry-run');

    if ($dryRun) {
        $this->warn('Running in dry-run mode — nothing will be saved.');
    }

    $this->info("Importing up to {$limit} posts from {$source}...");

    // ...

    return self::SUCCESS;
}
```

## Progress Bars

Display a progress bar for long-running operations:

```php
public function handle(): int
{
    $posts = Post::where('status', 'scheduled')
        ->where('published_at', '<=', now())
        ->get();

    $bar = $this->output->createProgressBar($posts->count());
    $bar->start();

    foreach ($posts as $post) {
        $post->update(['status' => 'published']);
        $bar->advance();
    }

    $bar->finish();
    $this->newLine();
    $this->info("{$posts->count()} posts published.");

    return self::SUCCESS;
}
```

## Scheduling Commands

Schedule module commands from within the service provider using `registerCommandSchedules`:

```php
protected function registerCommandSchedules(): void
{
    $this->app->booted(function () {
        $schedule = $this->app->make(\Illuminate\Console\Scheduling\Schedule::class);

        // Run every minute
        $schedule->command('blog:publish-scheduled')->everyMinute();

        // Run daily at midnight
        $schedule->command('blog:prune-drafts')->dailyAt('00:00');

        // Run on weekdays
        $schedule->command('blog:generate-sitemap')->weekdays()->at('03:00');
    });
}
```

Make sure the Laravel scheduler is running in your server's cron:

```bash
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```

## Calling Commands from Code

You can dispatch a command programmatically using `Artisan::call`:

```php
use Illuminate\Support\Facades\Artisan;

Artisan::call('blog:import-posts', [
    'source'  => 'api',
    '--limit' => 50,
]);
```

Or queue a command to run asynchronously:

```php
Artisan::queue('blog:import-posts', ['source' => 'api']);
```

## Asking for Confirmation

Prompt the user before performing a destructive operation:

```php
public function handle(): int
{
    if (! $this->confirm('This will delete all draft posts. Continue?')) {
        $this->info('Cancelled.');
        return self::SUCCESS;
    }

    Post::draft()->delete();
    $this->info('All draft posts deleted.');

    return self::SUCCESS;
}
```
