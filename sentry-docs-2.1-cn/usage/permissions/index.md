## Permissions // 权限

In this section we will cover how Sentry 2 permissions work and how the user
permission inheritance behaves.  
在本节中我们将介绍 Sentry 2 的权限是如何工作的，以及用户权限的继承是如何运作的。

Please be aware that Sentry 2 permissions works practically the same way as in
Sentry 1.  
请注意 Sentry 2 权限的工作方式几乎与 Sentry 1 中相同。

That said, here are the values that your groups and users permissions can have:  
也就是说，在这里你的分组和用户权限值可以是：

##### Groups Permissions // 分组权限

	0 : Deny  // 拒绝
	1 : Allow // 允许

##### Users Permissions // 用户权限

	-1 : Deny    // 拒绝
	 1 : Allow   // 允许
	 0 : Inherit // 继承

#### Permission Inheritance // 权限继承

Just as permissions are defined for groups and individual users, the permission
inheritance model depends on a user's group.  
正如权限被区分为分组和单个用户，权限继承的模式取决于用户的分组。

An Administrator can assign different permissions to a user than is assigned to a group:  
管理员可以分配不同的权限给用户，在已经为其分配了一个分组的情况下：

- If a user is not assigned a permission, or if the user is assigned a permission of 0 then the user will inherit permissions from the group
- 如果一个用户没有被指定一个权限的具体值，或者权限的值为 0，则用户将从所在分组继承权限。
- If a user is assigned a permission of -1 or 1, then the user's permission will override the group permission
- 如果一个用户被指定了 -1 或 1 的一个权限，则用户权限将覆盖分组权限。

> **Note:** Permission Inheritance only works for users permissions, an example
is provided on this page to help you
 better understand how this exactly works.  
 权限的继承仅适用于用户权限，在本页中我们将提供一个例子，以帮助你更好的理解这个特性是如何工作的。

##### Administrator Group // 管理员分组

Let's say you want to have two groups, an Administrator group and a Moderator
group, for each group you can define their own permissions:  
比方说你希望有两个分组，一个“管理员分组”和一个“主持人分组”，对于每个分组你都可以定义自己的权限：

	{
		"name" : "Administrator",
		"permissions" : {
			"user.create" : 1,
			"user.delete" : 1,
			"user.view"   : 1,
			"user.update" : 1
		}
	}

##### Moderator Group // 主持人分组

	{
		"name" : "Moderator",
		"permissions" : {
			"user.create" : 0,
			"user.delete" : 0,
			"user.view"   : 1,
			"user.update" : 1
		}
	}


And you have these three users, one as an Administrator, one as a Moderator
and the last one has the Administrator and Moderator groups assigned.  
并且你还有这样三个用户，一个作为管理员，一个作为主持人，以及最后一个被指定了“管理员”和“主持人”的分组。

##### User - John Rambo // 用户 - John Rambo

	{
		"id" : 1,
		"first_name" : "John",
		"last_name" : "Rambo",
		"groups" : ["administrator"],
		"permissions" : null
	}

###### Actions he can execute // 他可以执行的操作

This user has access to everything and can execute every action on your application.  
这个用户拥有一切访问权限，并且可以在你的应用程序中执行任何操作。

##### User - Rocky Balboa // 用户 - Rocky Balboa

	{
		"id" : 2,
		"first_name" : "Rocky",
		"last_name" : "Balboa",
		"groups" : ["moderator"],
		"permissions" : {
			"user.update" : 0
		}
	}

###### Actions he can execute // 他可以执行的操作

View and Update users  
查看以及更新用户信息

###### Actions he cannot execute // 他不能执行的操作

Create or Delete users  
创建或删除用户

> **Note:** We are using `Permission Inheritance` here, hence the
`user.update : 0` which means whatever you define on your group permission
this user permission will inherit that permission, which means that in this
case the user is allowed to update users.  
在这里我们使用了 `权限继承`，因此 `user.update : 0` 意味着无论你在分组中如何定义权限，这个用户都将继承那个权限，也就意味着在这种情况下允许这个用户“更新其他用户”。

##### User - Bruce Wayne // 用户 - Bruce Wayne

	{
		"id" : 3,
		"first_name" : "Bruce",
		"last_name" : "Wayne",
		"groups" : ["administrator", "moderator"],
		"permissions" : {
			"user.delete" : -1,
			"user.create" : 1
		}
	}

###### Actions he can execute // 他可以执行的操作

Create, Update and View users  
创建、更新以及查看用户

###### Actions he cannot execute // 他不能执行的操作

Delete users  
删除用户

Since this is a special user, mainly because this user has two assigned groups,
there are some things that you should know when assigning multiple groups to
an user.  
由于这是一个特殊的用户，主要是因为这个用户有两个指定的分组，某些情况下你应该知道何时分配多个分组给一个用户。

When a user has two or more groups assigned, if those groups have the same
permissions but different permission access's are assigned,
once one of those group permissions are denied, the user will be denied access
to that permission no matter what the other groups has as a permission value.  
当一个用户分配了两个或两个以上的分组，如果这些分组定义了相同的权限，但权限的赋值不同，一旦这些分组的权限之一被拒绝，用户将被拒绝拥有这个权限，无论在其它分组中对这个权限赋予了什么值。

Which means for you to `allow` a permission to this specific user, you need to
change the user permissions.

In this specific case, we allowed the user to create a new user by changing the
`user.create : 1` permission.

Notice that we are denying the `user.delete` permission of this user, in this
example, you don't need to do this, but let's say that in your group you are
allowing your users to delete other users, but for this specific user you don't
want him to be able to do that? To achieve this you `deny` this user permission
directly on the user, so no matter what your group permission looks like, this
user will never be able to delete other users.
