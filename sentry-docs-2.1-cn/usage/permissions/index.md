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

An Administrator can assign different permissions to a user than is assigned to a group:

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

	{
		"name" : "Administrator",
		"permissions" : {
			"user.create" : 1,
			"user.delete" : 1,
			"user.view"   : 1,
			"user.update" : 1
		}
	}

##### Moderator Group

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

##### User - John Rambo

	{
		"id" : 1,
		"first_name" : "John",
		"last_name" : "Rambo",
		"groups" : ["administrator"],
		"permissions" : null
	}

###### Actions he can execute

This user has access to everything and can execute every action on your application.

##### User - Rocky Balboa

	{
		"id" : 2,
		"first_name" : "Rocky",
		"last_name" : "Balboa",
		"groups" : ["moderator"],
		"permissions" : {
			"user.update" : 0
		}
	}

###### Actions he can execute

View and Update users

###### Actions he cannot execute

Create or Delete users

> **Note:** We are using `Permission Inheritance` here, hence the
`user.update : 0` which means whatever you define on your group permission
this user permission will inherit that permission, which means that in this
case the user is allowed to update users.

##### User - Bruce Wayne

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

###### Actions he can execute

Create, Update and View users

###### Actions he cannot execute

Delete users

Since this is a special user, mainly because this user has two assigned groups,
there are some things that you should know when assigning multiple groups to
an user.

When a user has two or more groups assigned, if those groups have the same
permissions but different permission access's are assigned,
once one of those group permissions are denied, the user will be denied access
to that permission no matter what the other groups has as a permission value.

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
