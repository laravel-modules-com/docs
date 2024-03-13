---
title: Tests
---

`phpunit.xml` is the configuration file for phpunit by default this file is configured to run tests only in the `/tests/Feature` and `/tests/Unit`

In order for your modules to be tested their paths need adding to this file, manually adding entries for each module is not desirable so instead use a wildcard include to include the modules dynamically:

```php
<testsuite name="Modules">
    <directory suffix="Test.php">./Modules/*/Tests/Feature</directory>
    <directory suffix="Test.php">./Modules/*/Tests/Unit</directory>
</testsuite>
```

Also ensure you've got the database connection set to sqlite and to use an in-memory database:

```php
<server name="DB_CONNECTION" value="sqlite"/>
<server name="DB_DATABASE" value=":memory:"/>
```

## Example phpunit.xml

The file would look like this

```php
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
>
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Modules">
            <directory suffix="Test.php">./Modules/*/Tests/Feature</directory>
            <directory suffix="Test.php">./Modules/*/Tests/Unit</directory>
        </testsuite>
    </testsuites>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
    <php>
        <server name="APP_ENV" value="testing"/>
        <server name="BCRYPT_ROUNDS" value="4"/>
        <server name="CACHE_DRIVER" value="array"/>
        <server name="DB_CONNECTION" value="sqlite"/>
        <server name="DB_DATABASE" value=":memory:"/>
        <server name="MAIL_MAILER" value="array"/>
        <server name="QUEUE_CONNECTION" value="sync"/>
        <server name="SESSION_DRIVER" value="array"/>
        <server name="TELESCOPE_ENABLED" value="false"/>
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
