## Groups // 分组

### Create a group // 创建一个分组

Creating new Groups is very easy and in this section you will learn how to create one.  
创建一个新的分组是非常简单的，在这个章节中你将学习如何创建一个分组。

Param       | Required | Default | Type  | Description
----------- | -------- | ------- | ----- | -----------------------------------
$attributes | true     | null    | array | The group attributes.
            |          |         |       | 分组的属性。

Below we'll list all the valid `keys` that you can pass through the `$attributes`.  
下面我们将列出你可以通过 `$attributes` 传递的所有有效的 `keys`。

Key         | Required | Type    | Description
----------- | -------- | ------- | --------------------------------------------
name        | true     | string  | The name of the group.
            |          |         | 分组名称。
permissions | false    | array   | The group permissions, pass a `key`/`value` pair.
            |          |         | 分组权限，传入一个 `键`/`值` 对数组。

#### Example

```php
try
{
	// Create the group
	$group = Sentry::createGroup(array(
		'name'        => 'Moderator',
		'permissions' => array(
			'admin' => 1,
			'users' => 1,
		),
	));
}
catch (Cartalyst\Sentry\Groups\NameRequiredException $e)
{
	echo 'Name field is required';
}
catch (Cartalyst\Sentry\Groups\GroupExistsException $e)
{
	echo 'Group already exists';
}
```

#### Exceptions // 异常

Below is a list of exceptions that this method can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                          | Description
-------------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\NameRequiredException      | If you don't provide the group name, this exception will be thrown.
                                                   | 如果你没有提供分组的名称，这个异常将被抛出。
Cartalyst\Sentry\Groups\GroupExistsException       | This exception will be thrown when the group you are trying to create already exists on your database.
                                                   | 当你试图创建数据库中已经存在的分组时，这个异常将被抛出。

### Update a group // 更新分组

#### Example

```php
try
{
	// Find the group using the group id
	$group = Sentry::findGroupById(1);

	// Update the group details
	$group->name = 'Users';
	$group->permissions = array(
		'admin' => 1,
		'users' => 1,
	);

	// Update the group
	if ($group->save())
	{
		// Group information was updated
	}
	else
	{
		// Group information was not updated
	}
}
catch (Cartalyst\Sentry\Groups\NameRequiredException $e)
{
	echo 'Name field is required';
}
catch (Cartalyst\Sentry\Groups\GroupExistsException $e)
{
	echo 'Group already exists.';
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Exceptions

Below is a list of exceptions that this method can throw.

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\NameRequiredException  | If you don't provide the group name, this exception will be thrown.
Cartalyst\Sentry\Groups\GroupExistsException   | This exception will be thrown when the group you are trying to create already exists on your database.
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.

### Delete a Group

#### Example

```php
try
{
	// Find the group using the group id
	$group = Sentry::findGroupById(1);

	// Delete the group
	$group->delete();
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Exceptions

Below is a list of exceptions that this method can throw.

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.

### Finding Groups

Sentry provides simple methods to find you your groups.

#### Find all the Groups

This will return all the groups.

```php
$groups = Sentry::findAllGroups();
```

#### Find a group by its ID

Find a group by it's ID.

```php
try
{
	$group = Sentry::findGroupById(1);
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Find a Group by it's Name

Find a group by it's name.

```php
try
{
	$group = Sentry::findGroupByName('admin');
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Exceptions

Below is a list of exceptions that the methods can throw.

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.

### Helpers

#### getPermissions()

Returns the permissions of a group.

```php
try
{
	// Find the group using the group id
	$group = Sentry::findGroupById(1);

	// Get the group permissions
	$groupPermissions = $group->getPermissions();
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group does not exist.';
}
```
