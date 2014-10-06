## Users // 用户

In this section we'll cover how you can create or register users, assign groups and permissions to users.  
在本节中，我们将介绍如何创建和注册用户，分配组和权限的用户。

### Sentry::createUser() // 创建用户

To create a new user you need to pass an `array()` of user fields into the `create()` method, please note, that the `login` field and the password are required, all the other fields are optional.  
要创建一个新的用户，你需要传递一个包含用户字段的 `array()` 到 `create()` 方法中，请注意，其中的 `login` 字段和密码字段是必须的，而所有其它的字段都是可选的。

Param        | Required | Default | Type  | Description
------------ | -------- | ------- | ----- | -----------------------------------
$credentials | true     | null    | array | The user credentials and attributes.
             |          |         |       | 用户凭证和属性。

#### Examples

Create a User and assign this new user an existing group.  
创建用户并分配现有的分组给新增的用户。

```php
try
{
	// Create the user
	$user = Sentry::createUser(array(
		'email'     => 'john.doe@example.com',
		'password'  => 'test',
		'activated' => true,
	));

	// Find the group using the group id
	$adminGroup = Sentry::findGroupById(1);

	// Assign the group to the user
	$user->addGroup($adminGroup);
}
catch (Cartalyst\Sentry\Users\LoginRequiredException $e)
{
	echo 'Login field is required.';
}
catch (Cartalyst\Sentry\Users\PasswordRequiredException $e)
{
	echo 'Password field is required.';
}
catch (Cartalyst\Sentry\Users\UserExistsException $e)
{
	echo 'User with this login already exists.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

Create a new user and set permissions on this user.  
创建一个新的用户并为其设置私有权限。

This example does pretty much the same as the previous one with the exception that we are not assigning him any group, but we are granting this user some permissions.  
这个例子几乎与前一个相同，除了我们没有给他指定任何分组这个例外，但我们授予了这个用户一些私有权限。

```php
try
{
	// Create the user
	$user = Sentry::createUser(array(
		'email'       => 'john.doe@example.com',
		'password'    => 'test',
		'activated'   => true,
		'permissions' => array(
			'user.create' => -1,
			'user.delete' => -1,
			'user.view'   => 1,
			'user.update' => 1,
		),
	));
}
catch (Cartalyst\Sentry\Users\LoginRequiredException $e)
{
	echo 'Login field is required.';
}
catch (Cartalyst\Sentry\Users\PasswordRequiredException $e)
{
	echo 'Password field is required.'
}
catch (Cartalyst\Sentry\Users\UserExistsException $e)
{
	echo 'User with this login already exists.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that this method can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\LoginRequiredException      | When you don't provide the required `login` field, this exception will be thrown.
                                                   | 当你没有提供必要的 `login` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\PasswordRequiredException   | When you don't provide the `password` field, this exception will be thrown.
                                                   | 当你没有提供 `密码` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserExistsException         | This exception will be thrown when the user you are trying to create already exists on your database.
                                                   | 当你试图创建数据库中已经存在的用户时，这个异常将被抛出。
Cartalyst\Sentry\Groups\GroupNotFoundException     | This exception will be thrown when the group that's being assigned to the user doesn't exist.
                                                   | 当被指定给用户的分组不存在时，这个异常将被抛出。

### Sentry::register() // 注册用户

Registering a user will require the user to be manually activated but you can bypass this passing a boolean of `true` as a second parameter.  
注册的用户将需要用户手动激活，但你可以通过传递一个布尔值为 `true` 的第二个参数来跳过激活步骤。

If the user already exists but is not activated, it will create a new activation code.  
如果该用户已经存在，但没有被激活，它会创建一个新的激活码。

Param        | Required | Default | Type    | Description
------------ | -------- | ------- | ------- | -----------------------------------
$credentials | true     | null    | array   | The user credentials and attributes.
             |          |         |         | 用户凭证和属性。
$activate    | false    | false   | boolean | Flag to wether activate the user or not.
             |          |         |         | 判断是否激活用户的标识。

#### Example

```php
try
{
	// Let's register a user.
	$user = Sentry::register(array(
		'email'    => 'john.doe@example.com',
		'password' => 'test',
	));

	// Let's get the activation code
	$activationCode = $user->getActivationCode();

	// Send activation code to the user so he can activate the account
}
catch (Cartalyst\Sentry\Users\LoginRequiredException $e)
{
	echo 'Login field is required.';
}
catch (Cartalyst\Sentry\Users\PasswordRequiredException $e)
{
	echo 'Password field is required.';
}
catch (Cartalyst\Sentry\Users\UserExistsException $e)
{
	echo 'User with this login already exists.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that this method can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\LoginRequiredException      | When you don't provide the required `login` field, this exception will be thrown.
                                                   | 当你没有提供必要的 `login` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\PasswordRequiredException   | When you don't provide the required `password` field, this exception will be thrown.
                                                   | 当你没有提供必要的 `密码` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserExistsException         | This exception will be thrown when the user you are trying to create already exists on your database.
                                                   | 当你试图创建一个数据库中已经存在的用户时，这个异常将被抛出。

### Update a User // 更新一个用户

Updating users information is very easy with Sentry, you just need to find the user you want to update and update their information. You can add or remove groups from users as well.  
使用 Sentry 更新用户信息是非常容易的，你只需要找到你想要更新的用户并更新他的信息。你同样可以增加或移除用户的分组。

#### Examples

In this example we are just updating the user information.  
在这个例子中我们仅更新了用户的信息。

> **Note:** If you provide another email address, and that email address is already registered in your system, the following Exception `Cartalyst\Sentry\Users\UserExistsException` will be thrown.  
> 如果你提供了另一个 email 地址，而这个 email 地址已经在系统中注册，则 `Cartalyst\Sentry\Users\UserExistsException` 异常将被抛出。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Update the user details
	$user->email = 'john.doe@example.com';
	$user->first_name = 'John';

	// Update the user
	if ($user->save())
	{
		// User information was updated
	}
	else
	{
		// User information was not updated
	}
}
catch (Cartalyst\Sentry\Users\UserExistsException $e)
{
	echo 'User with this login already exists.';
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

**Assign a new Group to a User // 指定一个新的分组给用户**

In this example we are assigning the provided Group to the provided User.  
在这个例子中我们分配一个指定的分组给一个指定的用户。

> **Note:** If the provided Group is not found an Exception `Cartalyst\Sentry\Groups\GroupNotFoundException` will be thrown.  
> 如果指定的分组不存在，则 `Cartalyst\Sentry\Groups\GroupNotFoundException` 异常将被抛出。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Find the group using the group id
	$adminGroup = Sentry::findGroupById(1);

	// Assign the group to the user
	if ($user->addGroup($adminGroup))
	{
		// Group assigned successfully
	}
	else
	{
		// Group was not assigned
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

**Remove a Group from the User // 移除用户的一个分组**

In this example we are removing the provided Group from the provided User.  
在这个例子中我们移除了指定用户的一个指定分组。

> **Note:** If the provided Group is not found an Exception `Cartalyst\Sentry\Groups\GroupNotFoundException` will be thrown.  
> 如果指定的分组不存在，则 `Cartalyst\Sentry\Groups\GroupNotFoundException` 异常将被抛出。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Find the group using the group id
	$adminGroup = Sentry::findGroupById(1);

	// Assign the group to the user
	if ($user->removeGroup($adminGroup))
	{
		// Group removed successfully
	}
	else
	{
		// Group was not removed
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

**Update the User details and assign a new Group // 更新用户的详细信息，并分配一个新的分组**

This is a combination of the previous examples, where we are updating the user information and assigning a new Group the provided User.  
这是前面的例子，在这里我们要更新的用户信息，并分配一个新的分组给用户。

> **Note:** If the provided Group is not found an Exception `Cartalyst\Sentry\Groups\GroupNotFoundException` will be thrown.  
> 如果指定的分组不存在，则 `Cartalyst\Sentry\Groups\GroupNotFoundException` 异常将被抛出。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Find the group using the group id
	$adminGroup = Sentry::findGroupById(1);

	// Assign the group to the user
	if ($user->addGroup($adminGroup))
	{
		// Group assigned successfully
	}
	else
	{
		// Group was not assigned
	}

	// Update the user details
	$user->email = 'john.doe@example.com';
	$user->first_name = 'John';

	// Update the user
	if ($user->save())
	{
		// User information was updated
	}
	else
	{
		// User information was not updated
	}
}
catch (Cartalyst\Sentry\Users\UserExistsException $e)
{
	echo 'User with this login already exists.';
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\LoginRequiredException      | When you don't provide the required `login` field, this exception will be thrown.
                                                   | 当你没有提供必要的 `login` 字段时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserExistsException         | This exception will be thrown when the user you are trying to create already exists in your database.
                                                   | 当你试图创建一个数据库中已存在的用户时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotFoundException       | If the provided user was not found, this exception will be thrown.
                                                   | 当指定的用户不存在时，这个异常将被抛出。
Cartalyst\Sentry\Groups\GroupNotFoundException     | This exception will be thrown when the group that's being assigned to the user doesn't exist.
                                                   | 当你试图创建一个数据库中已存在的分组时，这个异常将被抛出。

### Delete a user // 删除一个用户

Deleting users is very simple and easy.  
删除用户是非常简单并且容易的。

#### Example

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Delete the user
	$user->delete();
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
Cartalyst\Sentry\Users\UserNotFoundException       | If the provided user was not found, this exception will be thrown.
                                                   | 当指定的用户不存在时，这个异常将被抛出。

### Activating a User // 激活一个用户

User activation is very easy with Sentry, you need to first find the user you want to activate, then use the `attemptActivation()` method and provide the activation code, if the activation passes it will return `true` otherwise, it will return `false` .  
使用 Sentry 机会用户是非常容易的，你首先需要找到你想要激活的用户，然后使用 `attemptActivation()` 方法并提供激活码，如果激活成功它将返回 `true` 反之则返回 `false`。

> **Note:** If the user you are trying to activate, is already activated, the following Exception `Cartalyst\Sentry\Users\UserAlreadyActivatedException` will be thrown.  
> 如果你尝试激活的用户，已经被激活，则 `Cartalyst\Sentry\Users\UserAlreadyActivatedException` 异常将被抛出。

#### Example

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Attempt to activate the user
	if ($user->attemptActivation('8f1Z7wA4uVt7VemBpGSfaoI9mcjdEwtK8elCnQOb'))
	{
		// User activation passed
	}
	else
	{
		// User activation failed
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Users\UserAlreadyActivatedException $e)
{
	echo 'User is already activated.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                            | Description
---------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\UserAlreadyActivatedException | If the provided user is already activated, this exception will be thrown.
                                                     | 当指定的用户已经激活时，这个异常将被抛出。
Cartalyst\Sentry\Users\UserNotFoundException         | If the provided user was not found, this exception will be thrown.
                                                     | 当指定的用户不存在时，这个异常将被抛出。

### Reset a User Password // 重置用户密码

In this section you will learn how easy it is to reset a user password with Sentry 2.  
在本节中你将学习如何使用 Sentry 2 简单的重置一个用户的密码。

#### Step 1 // 步骤 1

The first step is to get a password reset code, to do this we use the
`getResetPasswordCode()` method.  
第一步是获得一个密码重置代码，为此我们使用 `getResetPasswordCode()` 方法。

##### Example

	try
	{
		// Find the user using the user email address
		$user = Sentry::findUserByLogin('john.doe@example.com');

		// Get the password reset code
		$resetCode = $user->getResetPasswordCode();

		// Now you can send this code to your user via email for example.
	}
	catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
	{
		echo 'User was not found.';
	}

#### Step 2 // 步骤 2

After your user received the password reset code you need to provide a way for them to validate that code, and reset their password.  
在你的用户收到重置代码后，你需要提供一种方式让他们去验证这个代码，并重置他的密码。

All the logic part on how you pass the reset password code is all up to you.  
验证成功后，重置密码的所有逻辑部分的代码都由你决定。

#### Example

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	// Check if the reset password code is valid
	// 检查重置密码的代码是否有效
	if ($user->checkResetPasswordCode('8f1Z7wA4uVt7VemBpGSfaoI9mcjdEwtK8elCnQOb'))
	{
		// Attempt to reset the user password
		// 尝试重置用户密码
		if ($user->attemptResetPassword('8f1Z7wA4uVt7VemBpGSfaoI9mcjdEwtK8elCnQOb', 'new_password'))
		{
			// Password reset passed
			// 密码重置通过
		}
		else
		{
			// Password reset failed
			// 密码重置失败
		}
	}
	else
	{
		// The provided password reset code is Invalid
		// 所提供的密码重置代码是无效的
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                     | Description
--------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\UserNotFoundException  | If the provided user was not found, this exception will be thrown.
                                              | 当指定的用户不存在时，这个异常将被抛出。

### Finding Users // 查找用户

Finding users can sometimes be difficult and harsh, well, Sentry provides you simple methods to find your users.  
查找用户有时是十分困难的，好了，Sentry 为你提供了简单的方法来找到你的用户。

#### Get the Current Logged in User // 获取当前登录的用户

Returns the user that's set with Sentry, does not check if a user is logged in or not. To do that, use `check()` instead.  
直接返回设置入 Sentry 中的用户，不检查特定的用户是否登录，如果需要，请使用 `check()` 代替。

```php
try
{
	// Get the current active/logged in user
	$user = Sentry::getUser();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	// User wasn't found, should only happen if the user was deleted
	// when they were already logged in or had a "remember me" cookie set
	// and they were deleted.
	// 用户未找到，应该仅发生在，当用户已登录后，或已经获得 "remember me" cookie 的设置后，其账号被删除时。
}
```

#### Find all the Users // 查找所有的用户

This will return all the users.  
这将返回所有的用户。

```php
$users = Sentry::findAllUsers();
```

#### Find all the Users with access to a permissions(s) // 查找所有具有指定权限的用户

Finds all users with access to a permission(s).  
查找所有具有指定权限的用户。

```php
// Feel free to pass a string for just one permission instead
// 当仅指定一个权限时，可以直接传递一个字符串作为参数
$users = Sentry::findAllUsersWithAccess(array('admin', 'other'));
```

#### Find all the Users in a Group // 查找分组下的所有用户

Finds all users assigned to a group.  
查找指定分组下的所有用户。

```php
$group = Sentry::findGroupByName('admin');

$users = Sentry::findAllUsersInGroup($group);
```

#### Find a User by their Credentials // 根据凭证查找用户

Find a user by an array of credentials, which must include the login column. Hashed fields will be hashed and checked against their value in the database.  
根据凭证数组查找一个用户，其中必须包含登录字段。哈希字段将被自动散列并比对数据库中的值。

```php
try
{
	$user = Sentry::findUserByCredentials(array(
		'email'      => 'john.doe@example.com',
		'password'   => 'test',
		'first_name' => 'John',
	));
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Find a User by their Id // 通过 ID 查找一个用户

Find a user by their ID.  
通过 ID 查找一个用户。

```php
try
{
	$user = Sentry::findUserById(1);
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Find a User by their Login Id // 通过登录字段查找一个用户

Find a user by their login ID.
通过登录字段查找一个用户。

```php
try
{
	$user = Sentry::findUserByLogin('john.doe@example.com');
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Find a User by their Activation Code // 通过激活码查找一个用户

Find a user by their registration activation code.  
通过注册时的激活码查找一个用户。

```php
try
{
	$user = Sentry::findUserByActivationCode('8f1Z7wA4uVt7VemBpGSfaoI9mcjdEwtK8elCnQOb');
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Find a User by their Reset Password Code // 通过密码重置代码查找一个用户

Find a user by their reset password code. 
通过密码重置代码查找一个用户。

```php
try
{
	$user = Sentry::findUserByResetPasswordCode('8f1Z7wA4uVt7VemBpGSfaoI9mcjdEwtK8elCnQOb');
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                    | Description
-------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Users\UserNotFoundException | If the provided user was not found, this exception will be thrown.
                                             | 当指定的用户不存在时，这个异常将被抛出。

### Helpers // 辅助

#### checkPassword() // 验证密码

Checks if the provided password matches the user's current password.  
验证提供的密码是否与用户当前的密码匹配。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserById(1);

	if($user->checkPassword('mypassword'))
	{
		echo 'Password matches.';
	}
	else
	{
		echo 'Password does not match.';
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### getGroups() // 获取用户的分组

Returns the user groups.  
返回用户的分组。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserByID(1);

	// Get the user groups
	$groups = $user->getGroups();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### getPermissions() // 获取用户的私有权限

Returns the user permissions.  
返回用户的私有权限。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserByID(1);

	// Get the user permissions
	$permissions = $user->getPermissions();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### getMergedPermissions() // 返回合并后的权限

Returns an array of merged permissions from groups and the user permissions.  
返回一个权限数组，它合并了用户的私有权限以及所在分组权限。

```php
try
{
	// Find the user using the user id
	$user = Sentry::getUserProvider()->findById(1);

	// Get the user permissions
	$permissions = $user->getMergedPermissions();
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### hasAccess($permission) // 检查用户是否被授予了指定的权限

Checks to see if a user been granted a certain permission. This includes any
permissions given to them by groups they may be apart of as well. Users may
also have permissions with a value of '-1'. This value is used to deny users of
permissions that may have been assigned to them from a group.  
检查用户是否被授予了指定的权限。这包括任何从他们的分组赋予的权限，它们可以是分开定义的。用户也可以拥有值为 '-1' 的私有权限。这个值用于拒绝用户拥有特定的权限，即使他们所在的分组给予了其它值。

Any user with `superuser` permissions automatically has access to everything,
regardless of the user permissions and group permissions.  
任何具有 `superuser` 权限的用户都将自动获得所有权限，无论用户定义了怎样的私有权限或分组。

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserByID(1);

	// Check if the user has the 'admin' permission. Also,
	// multiple permissions may be used by passing an array
	if ($user->hasAccess('admin'))
	{
		// User has access to the given permission
	}
	else
	{
		// User does not have access to the given permission
	}
}
catch (Cartalyst\Sentry\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### hasAnyAccess($permissions)

This method calls the `hasAccess()` method, and it is used to check if an user
has access to any of the provided permissions.

If one of the provided permissions is found it will return `true` even though the
user may not have access to the other provided permissions.

```php
try
{
	// Find the user using the user id
	$user = Sentry::getUserProvider()->findById(1);

	// Check if the user has the 'admin' and 'foo' permission.
	if ($user->hasAnyAccess(array('admin', 'foo')))
	{
		// User has access to one of the given permissions
	}
	else
	{
		// User does not have access to any of the given permissions
	}
}
catch (Cartalyst\Sentry\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### isActivated()

Checks if a user is activated.

```php
try
{
	// Find the user
	$user = Sentry::findUserByLogin('jonh.doe@example.com');

	// Check if the user is activated or not
	if ($user->isActivated())
	{
		// User is Activated
	}
	else
	{
		// User is Not Activated
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### isSuperUser()

Returns if the user is a super user, it means, that has access to everything regardless of permissions.

```php
try
{
	// Find the user
	$user = Sentry::findUserByLogin('jonh.doe@example.com');

	// Check if this user is a super user
	if ($user->isSuperUser())
	{
		// User is a super user
	}
	else
	{
		// User is not a super user
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
```

#### inGroup($group)

Checks if a user is in a certain group.

```php
try
{
	// Find the user using the user id
	$user = Sentry::findUserByID(1);

	// Find the Administrator group
	$admin = Sentry::findGroupByName('Administrator');

	// Check if the user is in the administrator group
	if ($user->inGroup($admin))
	{
		// User is in Administrator group
	}
	else
	{
		// User is not in Administrator group
	}
}
catch (Cartalyst\Sentry\Users\UserNotFoundException $e)
{
	echo 'User was not found.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```
