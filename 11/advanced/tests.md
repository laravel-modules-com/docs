---
title: Tests
order: 6
---

`phpunit.xml` is the configuration file for PHPUnit by default this file is configured to run tests only in the `/tests/Feature` and `/tests/Unit`

For your modules to be tested their paths need to be added to this file, manually adding entries for each module is not desirable so instead use a wildcard include to include the modules dynamically:

```php
<testsuite name="Modules">
    <directory suffix="Test.php">./Modules/*/tests/Feature</directory>
    <directory suffix="Test.php">./Modules/*/tests/Unit</directory>
</testsuite>
```

Also, ensure you've got the database connection set to `sqlite` and to use an in-memory database:

```php
<server name="DB_CONNECTION" value="sqlite"/>
<server name="DB_DATABASE" value=":memory:"/>
```

Add modules into code coverage by adding paths to the modules inside an include tag, use exclude to exclude certain folders from being scanned.

```
<source>
    <include>
        <directory suffix=".php">./app</directory>
        <directory suffix=".php">./Modules</directory>
    </include>
    <exclude>
        <directory suffix=".php">./Modules/*/config</directory>
        <directory suffix=".php">./Modules/*/database</directory>
        <directory suffix=".php">./Modules/*/resources</directory>
        <directory suffix=".php">./Modules/*/routes</directory>
        <directory suffix=".php">./Modules/*/tests</directory>
    </exclude>
</source>
```

## Example phpunit.xml

The file would look like this

```php
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.0/phpunit.xsd" bootstrap="vendor/autoload.php"
         colors="true" cacheDirectory=".phpunit.cache">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Modules">
          <directory suffix="Test.php">./Modules/*/tests/Feature</directory>
          <directory suffix="Test.php">./Modules/*/tests/Unit</directory>
        </testsuite>
    </testsuites>
    <source>
        <include>
            <directory suffix=".php">./app</directory>
            <directory suffix=".php">./Modules</directory>
        </include>
        <exclude>
            <directory suffix=".php">./Modules/*/config</directory>
            <directory suffix=".php">./Modules/*/database</directory>
            <directory suffix=".php">./Modules/*/resources</directory>
            <directory suffix=".php">./Modules/*/routes</directory>
            <directory suffix=".php">./Modules/*/tests</directory>
        </exclude>
    </source>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="BCRYPT_ROUNDS" value="4"/>
        <env name="CACHE_STORE" value="array"/>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="MAIL_MAILER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="TELESCOPE_ENABLED" value="false"/>
    </php>
</phpunit>
```

Now when phpunit is run tests from modules and the global tests folder will run.

## Run test for a single module

When you run phpunit:

```php
vendor/bin/phpunit
```

All test will run. 

## Run single test

If you want to only run a single test method, class or module you can use the filter flag:

```php
vendor/bin/phpunit --filter 'contacts'
```

This would run all tests inside the Contacts module.

Run a single test method:

```php
vendor/bin/phpunit --filter 'can_delete_contact'
```

This would match a test

```php
/** @test */
public function can_delete_contact(): void
{
    $this->authenticate();
    $contact = Contact::factory()->create();
    $this->delete(route('app.contacts.delete', $contact->id))->assertRedirect(route('app.contacts.index'));

    $this->assertDatabaseCount('contacts', 0);
}
```

Test a full test file:

```php
vendor/bin/phpunit --filter 'ContactTest' 
```

## Use PestPHP

Using PestPHP [https://pestphp.com](https://pestphp.com).

Often you will want to run Pest testcase class, import it at the top of your file. 

```php
uses(Tests\TestCase::class);
```

When using Pest you don't need classes the following would be a valid file:

```php
<?php

use Modules\Contacts\Models\Contact;

uses(Tests\TestCase::class);

test('can see contact list', function() {
    $this->authenticate();
   $this->get(route('app.contacts.index'))->assertOk();
});

test('can delete contact', function() {
    $this->authenticate();
    $contact = Contact::factory()->create();
    $this->delete(route('app.contacts.delete', $contact->id))->assertRedirect(route('app.contacts.index'));

    $this->assertDatabaseCount('contacts', 0);
});
```

Run test suite:

```php
vendor/bin/pest
```

If you want to only run a single test method, class or module you can use the filter flag:

```php
vendor/bin/pest --filter 'contacts'
```

This would match a test

```php
test('can_delete_contact', function() {
    $this->authenticate();
    $contact = Contact::factory()->create();
    $this->delete(route('app.contacts.delete', $contact->id))->assertRedirect(route('app.contacts.index'));

    $this->assertDatabaseCount('contacts', 0);
});
```
