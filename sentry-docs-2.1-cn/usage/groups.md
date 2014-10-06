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
	// 通过 分组ID 查找分组
	$group = Sentry::findGroupById(1);

	// Update the group details
	// 更新分组详情
	$group->name = 'Users';
	$group->permissions = array(
		'admin' => 1,
		'users' => 1,
	);

	// Update the group
	// 更新分组
	if ($group->save())
	{
		// Group information was updated
		// 分组信息已被更新
	}
	else
	{
		// Group information was not updated
		// 分组信息未被更新
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
下面是该方法可能抛出的异常的列表。

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\NameRequiredException  | If you don't provide the group name, this exception will be thrown.
                                               | 如果你没有提供分组的名称，这个异常将被抛出。
Cartalyst\Sentry\Groups\GroupExistsException   | This exception will be thrown when the group you are trying to create already exists on your database.
                                               | 当你试图创建数据库中已经存在的分组时，这个异常将被抛出。
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.
                                               | 如果指定的分组没有找到，这个异常将被抛出。

### Delete a Group // 删除一个分组

#### Example

```php
try
{
	// Find the group using the group id
	// 通过 分组ID 查找分组
	$group = Sentry::findGroupById(1);

	// Delete the group
	// 删除分组
	$group->delete();
}
catch (Cartalyst\Sentry\Groups\GroupNotFoundException $e)
{
	echo 'Group was not found.';
}
```

#### Exceptions // 异常

Below is a list of exceptions that this method can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.
                                               | 如果指定的分组没有找到，这个异常将被抛出。

### Finding Groups // 查找分组

Sentry provides simple methods to find you your groups.
Sentry 提供了一个简单的方法来查找你的分组。

#### Find all the Groups // 查找所有的分组

This will return all the groups.  
这将返回所有的分组。

```php
$groups = Sentry::findAllGroups();
```

#### Find a group by its ID // 通过“分组ID”查找一个分组

Find a group by it's ID.  
通过“分组ID”查找一个分组。

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

#### Find a Group by it's Name // 通过“分组名称”查找一个分组

Find a group by it's name.  
通过“分组名称”查找一个分组。

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

#### Exceptions // 异常

Below is a list of exceptions that the methods can throw.  
下面是该方法可能抛出的异常的列表。

Exception                                      | Description
---------------------------------------------- | --------------------------------------------------------------------------------
Cartalyst\Sentry\Groups\GroupNotFoundException | If the provided group was not found, this exception will be thrown.
                                               | 如果指定的分组没有找到，这个异常将被抛出。

### Helpers // 辅助

#### getPermissions() // 返回一个分组的权限

Returns the permissions of a group.  
返回一个分组的权限。

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
