## Throttle // 限制

### Disable the Throttling Feature // 禁止限制功能

Disables the throttling feature.  
禁止限制功能。

Can be done on the throttle provider (global) level or on a throttle instance itself.  
可以通过 throttle provider (全局命名空间) 或 throttle 实例本身来完成。

#### Example

```php
// Get the Throttle Provider
$throttleProvider = Sentry::getThrottleProvider();

// Disable the Throttling Feature
$throttleProvider->disable();
```

### Enable the Throttling Feature // 启用限制功能

Enables the throttling feature.  
启用限制功能。

Can be done on the throttle provider (global) level or on a throttle instance itself.  
可以通过 throttle provider (全局命名空间) 或 throttle 实例本身来完成。

#### Example

```php
// Get the Throttle Provider
$throttleProvider = Sentry::getThrottleProvider();

// Enable the Throttling Feature
$throttleProvider->enable();
```

### Check the Throttling Feature Status // 检查限制功能是否开启

Checks to see if the throttling feature is enabled or disabled.  
检查限制功能是否开启。

#### Example

```php
// Get the Throttle Provider
$provider = Sentry::getThrottleProvider();

// Check if the Throttling feature is enabled or disabled
if($provider->isEnabled())
{
	// The Throttling Feature is Enabled
}
else
{
	// The Throttling Feature is Disabled
}
```

### User Throttling // 针对用户的限制

#### Ban user(s) // 禁用用户

Bans the user associated with the throttle.  
禁用与 throttle 相关联的用户。

```php
try
{
	// Find the user using the user id
	$throttle = Sentry::findThrottlerByUserId(1);

	// Ban the user
	$throttle->ban();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Unban user(s) // 解禁用户

Unbans the user associated with the throttle.  
解除禁用与 throttle 相关联的用户。

```php
try
{
	// Find the user using the user id
	$throttle = Sentry::findThrottlerByUserId(1);

	// Unban the user
	$throttle->unBan();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Check if a User is Banned // 检查用户是否已被禁用

Checks to see if the user is banned.  
检查用户是否已被禁用。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	if($banned = $throttle->isBanned())
	{
		// User is Banned
	}
	else
	{
		// User is not Banned
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Suspend user(s) // 暂时挂起用户

Suspends a user temporarily. Length of the suspension is set by the driver or setSuspensionTime($minutes).  
暂时挂起用户，该挂起时长取决于其驱动设置或 `setSuspensionTime($minutes)` 方法。

```php
try
{
	// Find the user using the user id
	$throttle = Sentry::findThrottlerByUserId(1);

	// Suspend the user
	$throttle->suspend();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Unsuspend user(s) // 取消用户挂起状态

Unsuspends a login. This also clears all previous attempts by the specified login if they were suspended.  
取消挂起状态。这也将清除所有之前的尝试记录，如果他曾经被挂起。

```php
try
{
	// Find the user using the user id
	$throttle = Sentry::findThrottlerByUserId(1);

	// Unsuspend the user
	$throttle->unsuspend();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Check if a User is Suspended // 检查用户是否已被挂起

Checks to see if the user is suspended.  
检查用户是否已被挂起。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	if($suspended = $throttle->isSuspended())
	{
		// User is Suspended
	}
	else
	{
		// User is not Suspended
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Set the User Suspension Time // 设置用户挂起时长

Sets the length of the suspension.  
设置用户挂起时长。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$throttle->setSuspensionTime(10);
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Get the User Suspension Time // 获取用户挂起时长

Retrieves the length of the suspension time set by the throttling driver.  
获取用户挂起时长。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$suspensionTime = $throttle->getSuspensionTime();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Add a Login Attempt // 累加一个登录尝试次数

Adds an attempt to the throttle object.  
累加一个登录尝试次数。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$throttle->addLoginAttempt();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Get Login Attempts // 获取登录尝试次数

Retrieves the number of attempts a user currently has tried. Checks suspension time to see if login attempts can be reset. This may happen if the suspension time was (for example) 10 minutes however the last login was 15 minutes ago, attempts will be reset to 0.  
获取用户当前已尝试登录的次数。检查挂起时间内若尝试登录则尝试次数可以被重置。这可能会出现一种情况，如果挂起时间为 (例如) 十分钟，然而最后登录在十五分钟前，则尝试次数将被清零。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$attempts = $throttle->getLoginAttempts();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Clear Login Attempts // 清除登录尝试次数

Clears all login attempts, it also unsuspends them. This does not unban a login.  
清除所有的登录尝试次数，它也将解除他们的挂起状态。但这不会解除禁用状态。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$throttle->clearLoginAttempts();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Check the User Throttle Status // 检查用户的限制状态

Checks the login throttle status and throws a number of Exceptions upon failure.  
检查登录限制状态，并抛出一些对应的异常。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	if ($throttle->check())
	{
		echo 'Good to go.';
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Throttling\UserSuspendedException $e)
{
	$time = $throttle->getSuspensionTime();

	echo "User is suspended for [$time] minutes.";
}
catch (Cartalyst\Sentry\Throttling\UserBannedException $e)
{
	ehco 'User is banned.';
}
```

#### Set Attempt Limit // 设置尝试次数

Sets the number of attempts allowed before suspension.  
设置挂起之前所允许的尝试次数。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$throttle->setAttemptLimit(3);
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Get Attempt Limit // 获取尝试次数

Retrieves the number of attempts allowed by the throttle object.  
获取 throttle 对象允许的尝试次数。

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);

	$attemptLimit = $throttle->getAttemptLimit();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Throttle\UserNotFoundException    | If the provided user was not found, this exception will be thrown.
                                                   | 如果提供的用户不存在，这个异常将被抛出。
Cartalyst\Sentry\Throttling\UserSuspendedException | When the provided user is suspended, this exception will be thrown.
                                                   | 当提供的用户被挂起时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserBannedException         | When the provided user is banned, this exception will be thrown.
                                                   | 当提供的用户被禁用时，这个异常将被抛出。

### Find User(s) // 查找用户

#### Find a User by their Id // 通过 ID 查找用户

Retrieves a throttle object based on the user ID provided. Will always retrieve a throttle object.

```php
try
{
	$throttle = Sentry::findThrottlerByUserId(1);
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Find a User by their Login

Retrieves a throttle object based on the user login provided. Will always retrieve a throttle object.

```php
try
{
	$throttle = Sentry::findThrottlerByUserLogin('john.doe@example.com');
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Exceptions

Below is a list of exceptions that the methods can throw.

Exception                                    | Description
-------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\UserNotFoundException | If the provided user was not found, this exception will be thrown.
