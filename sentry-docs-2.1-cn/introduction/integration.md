# Integration 安装

> **Note:** The database schema is located under `vendor/cartalyst/sentry/schema/mysql.sql`  
> **Note:** 数据库架构位于 `vendor/cartalyst/sentry/schema/mysql.sql` 文件中

## Laravel 4

After you have installed the package, just follow the instructions.

Sentry has optional support for Laravel 4 and it comes bundled with a Service Provider and a Facade for easy integration.

After installing the package, open your Laravel config file `app/config/app.php` and add the following lines.

In the `$providers` array add the following service provider for this package.

	'Cartalyst\Sentry\SentryServiceProvider',

In the `$aliases` array add the following facade for this package.

	'Sentry' => 'Cartalyst\Sentry\Facades\Laravel\Sentry',

### Migrations

	php artisan migrate --package=cartalyst/sentry

### Configuration

After installing, you can publish the package configuration file into your application by running the following command:

	php artisan config:publish cartalyst/sentry

This will publish the config file to `app/config/packages/cartalyst/sentry/config.php` where you can modify the package configuration.

## CodeIgniter 3.0-dev

After you have installed the package, just follow the instructions.

Visit `application/config/config.php` and right down the bottom, add the following:

```php
class_alias('Cartalyst\Sentry\Facades\CI\Sentry', 'Sentry');
```

This will allow you to use Sentry as normal in CodeIgniter and sets up dependencies required for Sentry to run smoothly within the CI environment.

> **Note:**: You must be running your database using the `PDO` driver (though this would be recommended anyway). Configuration for a MySQL database running PDO could be as follows (in `application/config/database.php`):

```php
// Ensure the active group is the default config.
// Sentry always runs off your application's default
// database connection.
$active_group = 'default';

// Setup the default config
$db['default'] = array(

	// PDO requires the host, dbname and charset are all specified in the "dsn",
	// so we'll go ahead and do these now.
	'dsn'	   => 'mysql:host=localhost;dbname=cartalyst_sentry;charset=utf8;',
	'hostname' => 'localhost',
	'username' => 'root',
	'password' => 'root',
	'database' => '',
	'dbdriver' => 'pdo',
	'dbprefix' => '',
	'pconnect' => TRUE,
	'db_debug' => TRUE,
	'cache_on' => FALSE,
	'cachedir' => '',
	'char_set' => 'utf8',
	'dbcollat' => 'utf8_general_ci',
	'swap_pre' => '',
	'autoinit' => TRUE,
	'encrypt'  => FALSE,
	'compress' => FALSE,
	'stricton' => FALSE,
	'failover' => array()
);
```

## FuelPHP 1.x

After you have installed the package, just follow the instructions.

You must put the following in `app/bootstrap.php` below `Autoloader::register()`:

```php
// Enable composer based autoloading
require APPPATH.'vendor/autoload.php';
```

Great! You now have composer working with FuelPHP.

Just one more step is involved now, right at the bottom of that same file, `app/bootstrap.php`, put the following:

```php
class_alias('Cartalyst\Sentry\Facades\FuelPHP\Sentry', 'Sentry');
```

This will mean you can use the FuelPHP Sentry facade as the class `Sentry`. Vòila! Sentry automatically works with your current database configuration, there is no further setup required.

> **Note:**: Sentry will always run off the default database connection, so ensure this is working. We may look at adding support for alternate connections in the future however it is not implemented at this stage. Pull requests are welcome.

## Native

```php
// Include the composer autoload file
include_once "vendor/autoload.php";

// Import the necessary classes
use Illuminate\Database\Capsule\Manager as Capsule;

// Create the Sentry alias
class_alias('Cartalyst\Sentry\Facades\Native\Sentry', 'Sentry');

// Create a new Database connection
$capsule = new Capsule;

$capsule->addConnection([
    'driver'    => 'mysql',
    'host'      => 'localhost',
    'database'  => 'database',
    'username'  => 'root',
    'password'  => '',
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
]);

$capsule->bootEloquent();
```

The integration is done and you can now use all the available methods, here's an example:

```php
// Find a user using the user email address
$user = Sentry::findUserByLogin('john.doe@example.com');
```
