## Authentication // 认证

In this section, we will cover authentication and login.  
在本节中，我们将介绍认证和登录。

### Sentry::authenticate() // 基于凭证认证一个用户

Authenticates a user based on the provided credentials.  
基于凭证认证一个用户。

If the authentication was successful, password reset fields and any invalid authentication attempts will be cleared.  
如果认证成功，“密码重置字段”和任何“无效认证尝试”将被清空。

Param        | Required | Default | Type  | Description
------------ | -------- | ------- | ----- | -----------------------------------
$credentials | true     | null    | array | Array that should contain the user credentials like `email` and `password`.
             |          |         |       | 数组内必须包含用户凭证，例如 `email` 和 `password`。
$remember    | false    | false   | bool  | Flag to wether Sentry should remember the user. It sets a Cookie.
             |          |         |       | 它将设置一个 Cookie，用来标记 Sentry 是否应该记住这个用户。

#### Example

```php
try
{
	// Login credentials
	// 登录凭证
	$credentials = array(
		'email'    => 'john.doe@example.com',
		'password' => 'password',
	);

	// Authenticate the user
	// 认证用户
	$user = Sentry::authenticate($credentials, false);
}
catch (Cartalyst\Sentry\Users\LoginRequiredException $e)
{
	echo 'Login field is required.';
}
catch (Cartalyst\Sentry\Users\PasswordRequiredException $e)
{
	echo 'Password field is required.';
}
catch (Cartalyst\Sentry\Users\WrongPasswordException $e)
{
	echo 'Wrong password, try again.';
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Users\UserNotActivatedException $e)
{
	echo 'User is not activated.';
}

// The following is only required if the throttling is enabled
// 仅当 throttling（限制） 开启时才需要以下内容
catch (Cartalyst\Sentry\Throttling\UserSuspendedException $e)
{
	echo 'User is suspended.';
}
catch (Cartalyst\Sentry\Throttling\UserBannedException $e)
{
	echo 'User is banned.';
}
```

#### Exceptions 异常

In this section, we provide a list of all exceptions that are thrown by Sentry.  
在本节中，我们将提供 Sentry 抛出的所有异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\LoginRequiredException      | When you don't provide the required `login` field, this exception will be thrown.
                                                   | 当你没有提供必须的 `login` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\PasswordRequiredException   | When you don't provide the `password` field, this exception will be thrown.
                                                   | 当你没有提供 `password` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotActivatedException   | When the provided user is not activated, this exception will be thrown.
                                                   | 当提供的用户未被激活时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotFoundException       | If the provided user was not found, this exception will be thrown.
                                                   | 当提供的用户没有找到时，这个异常将被抛出。
Cartalyst\Sentry\Users\WrongPasswordException      | When the provided password is incorrect, this exception will be thrown.
                                                   | 当提供的密码错误时，这个异常将被抛出。
Cartalyst\Sentry\Throttling\UserSuspendedException | When the provided user is suspended, this exception will be thrown.
                                                   | 当提供的用户被暂停使用时，这个异常将被抛出。
Cartalyst\Sentry\Throttling\UserBannedException    | When the provided user is banned, this exception will be thrown.
                                                   | 当提供的用户被禁用时，这个异常将被抛出。

### Sentry::authenticateAndRemember() // 基于凭证认证并记住用户

Authenticates and Remembers a user based on credentials. This is an helper function for the `authenticate()` which sets the `$remember` flag to true so the user is remembered (using a cookie). This is the "remember me" you are used to see on web sites.  
基于凭证认证并记住用户。这是 `authenticate()` 的辅助方法，将其 `$remember` 参数标记为 true ，因此用户将被记住（使用一个 cookie）。你可以用它实现在网站上看到的“记住我”的功能。

#### Example

	Sentry::authenticateAndRemember($credentials);

### Sentry::login() // 登录所提供的用户

Logs in the provided user and sets properties in the session.  
登录所提供的用户并设置其属性于 session 中。

If the login is successful, password reset fields and any invalid authentication attempts will be cleared.  
如果登录成功，“密码重置字段”和任何“无效认证尝试”将被清空。

Param        | Required | Default | Type   | Description
------------ | -------- | ------- | ------ | -----------------------------------
$user        | true     | null    | object | A user object `Cartalyst\Sentry\Users\UserInterface`.
             |          |         |        | 一个实现了 `Cartalyst\Sentry\Users\UserInterface` 接口的 user 对象。
$remember    | false    | false   | bool   | Flag to wether Sentry should remember the user. It sets a Cookie.
             |          |         |        | 它将设置一个 Cookie，用来标记 Sentry 是否应该记住这个用户。

#### Example

```php
try
{
	// Find the user using the user id
	// 通过 user id 查找用户
	$user = Sentry::findUserById(1);

	// Log the user in
	// 登录用户
	Sentry::login($user, false);
}
catch (Cartalyst\Sentry\Users\LoginRequiredException $e)
{
	echo 'Login field is required.';
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User not found.';
}
catch (Cartalyst\Sentry\Users\UserNotActivatedException $e)
{
	echo 'User not activated.';
}

// Following is only needed if throttle is enabled
// 仅当 throttling（限制） 开启时才需要以下内容
catch (Cartalyst\Sentry\Throttling\UserSuspendedException $e)
{
	$time = $throttle->getSuspensionTime();

	echo "User is suspended for [$time] minutes.";
}
catch (Cartalyst\Sentry\Throttling\UserBannedException $e)
{
	echo 'User is banned.';
}
```

#### Exceptions

Below is a list of exceptions that this method can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\LoginRequiredException      | When you don't provide the required `login` field, this exception will be thrown.
                                                   | 当你没有提供必须的 `login` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotFoundException       | If the provided user was not found, this exception will be thrown.
                                                   | 当提供的用户没有找到时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotActivatedException   | When the provided user is not activated, this exception will be thrown.
                                                   | 当提供的用户未被激活时，这个异常将被抛出。
Cartalyst\Sentry\Throttling\UserSuspendedException | When the provided user is suspended, this exception will be thrown.
                                                   | 当提供的用户被暂停使用时，这个异常将被抛出。
Cartalyst\Sentry\Throttling\UserBannedException    | When the provided user is banned, this exception will be thrown.
                                                   | 当提供的用户被禁用时，这个异常将被抛出。

### Sentry::loginAndRemember() // 基于凭证登录并记住用户

Logs in and Remembers a user based on credentials. This is an helper function for the `login()` which sets the `$remember` flag to true so the user is remembered (using a cookie). This is the "remember me" you are used to see on web sites.  
基于凭证登录并记住用户。这是 `login()` 的辅助方法，将其 `$remember` 参数标记为 true ，因此用户将被记住（使用一个 cookie）。你可以用它实现在网站上看到的“记住我”的功能。

#### Example

```php
Sentry::loginAndRemember($user);
```

### Sentry::logout() // 登出用户

The logout method logs the user out and destroys all Sentry sessions / cookies for the user.  
logout 方法将登出用户，并销毁所有与 Sentry 有关的用户 sessions / cookies。

This method does **not** fail or throw any Exceptions if there is no user logged in.  
如果用户没有登录，这个方法 **并不会** 产生错误或抛出任何异常。

#### Example

```php
Sentry::logout();
```

### Sentry::check() // 判断用户是否登录

The check method returns a `bool` of whether the user is logged in or not, or if the user is not activated.  
check 方法返回一个 `bool` 值来判断用户是否登录，或者，如果用户没有激活。

If it's logged in, the current User is set in Sentry so you can easily access it via `getUser()`.  
如果已经登录，则当前的用户将被设置进 Sentry，因此你可以轻松的通过 `getUser()` 来访问它。

A user must be activated to pass `check()`.  
一个用户必须激活才能够通过 `check()` 验证。

#### Example

```php
if ( ! Sentry::check())
{
	// User is not logged in, or is not activated
	// 用户“未登录”或“未激活”
}
else
{
	// User is logged in
	// 用户已经登录
}
```
