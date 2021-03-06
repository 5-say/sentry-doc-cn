# Installation // 安装

The best and easiest way to install Sentry is with [Composer](http://getcomposer.org/)  
sentry 最好且最简单的安装方式是使用 [Composer](http://getcomposer.org/)。

## Preparation // 准备

Open your composer.json file and add the following to the require array:  
打开 composer.json 文件，添加下面的代码到 require 数组：

```php
"cartalyst/sentry": "2.1.*"
```

> **Note:** Make sure that after the required changes your composer.json file is valid by running composer validate.  
> **Note** 通过 composer validate 命令进行验证，以确保你对 composer.json 文件的更改是合法的。

## Install the dependencies // 安装依赖包

Run Composer to install or update the new requirement.  
运行 Composer 来安装或更新这些新的必要组件。

	php composer install

or // 或者

	php composer update

Now you are able to require the vendor/autoload.php file to autoload the package.  
现在你可以包含 vendor/autoload.php 文件来自动加载这个资源包了。

# Integration // 综合

> **Note:** The database schema is located under `vendor/cartalyst/sentry/schema/mysql.sql`  
> **Note:** 数据库架构位于 `vendor/cartalyst/sentry/schema/mysql.sql` 文件中。

## Laravel 4

After you have installed the package, just follow the instructions.  
在你安装这个资源包后，只需要根据提示操作。

Sentry has optional support for Laravel 4 and it comes bundled with a Service Provider and a Facade for easy integration.  
Sentry 捆绑了可选的 Service Provider 和 Facade 支持，使 Laravel 4 的集成更加容易。

After installing the package, open your Laravel config file `app/config/app.php` and add the following lines.  
安装这个资源包后，打开你 Laravel 的 config 文件 `app/config/app.php` 添加以下几行。

In the `$providers` array add the following service provider for this package.  
在 `$providers` 数组内为这个资源包添加下面的 service provider。

	'Cartalyst\Sentry\SentryServiceProvider',

In the `$aliases` array add the following facade for this package.  
在 `$aliases` 数组内为这个资源包添加下面的  facade。

	'Sentry' => 'Cartalyst\Sentry\Facades\Laravel\Sentry',

### Migrations // 迁移

	php artisan migrate --package=cartalyst/sentry

### Configuration // 配置

After installing, you can publish the package configuration file into your application by running the following command:  
安装完成后，你可以运行下面的命令，将这个资源包的配置文件发布到你的应用程序中：

	php artisan config:publish cartalyst/sentry

This will publish the config file to `app/config/packages/cartalyst/sentry/config.php` where you can modify the package configuration.  
这将发布配置文件到 `app/config/packages/cartalyst/sentry/config.php` 在这里你可以修改这个资源包的配置。

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
集成完毕后，你现在可以使用所有可用的方法，下面是一个例子：

```php
// Find a user using the user email address
// 使用“用户的 email 地址”来查找一个用户
$user = Sentry::findUserByLogin('john.doe@example.com');
```
