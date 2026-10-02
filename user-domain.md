## User & Domain

### Introduction
The domain is the entity that groups all the Danalock components in the same system (users, locks, AFI devices, etc.). A domain may have the following associated entities:

- **Users**: a user belongs to one or more domains and has a unique identity.
- **AFI Devices**: afi device can be a lock or a Danalock's auxiliary device such as DanaPad, DanaBridge or Universal Module. An afi device can belong to only one domain.
- **Groups**: groups are collections of locks or users that allow you to provide group permissions.
- **Links**: links are what binds locks/devices and users together, and effectively what grants a user access to a device.
- **Link Profiles**: link profiles determine what permissions a user has when being granted access to a lock.
- **Calendars**: a calendar is used to specify when an access is valid.
- **EKey (V3/V2)**: a collection of operations related to locks, users, groups and logs (create, list, register, etc..). V3 and V2 identifies the lock device version.
>Ekey V2 is DEPREACTED

&nbsp;
&nbsp;
### User roles

Roles grant different permissions in a domain.

| Role          | Description                    | Limitations                      |
|---------------|--------------------------------|----------------------------------|
| Owner         | Full control over domain       | cannot be deleted by other users |
| Administrator | Full control over domain       | cannot remove the domain owner   |
| Installer     | Restricted control over domain | cannot manage users or groups    |
| User          | No access over domain          | can only operate lock device     |

Each role has a default link profile, so if a link is created without passing the `link_profile` parameter, default `User` link profile will be assigned.

It's very important the difference between **Domain permissions** and **Link profile**.
- Domain permission refers to the actions that a user can execute in a domain.
- [Link profile](https://github.com/Danalock/BackendDocs/blob/danaserver-api-docs/danaserver-api/device-management.md#lock-device) is what binds locks and users together, and what grants a user access to a device.

**Domain permissions:**


| Action            | Administrator | Installer | User    |
|-------------------|---------------|-----------|---------|
| Enroll devices    | &check;       | &check;   | &cross; |
| Disenroll devices | &check;       | &check;   | &cross; |
| Create users      | &check;       | &cross;   | &cross; |
| Invite users      | &check;       | &cross;   | &cross; |
| Remove users      | &check;       | &check;   | &cross; |
| Create groups     | &check;       | &check;   | &cross; |
| Modify groups     | &check;       | &cross;   | &cross; |
| Remove groups     | &check;       | &cross;   | &cross; |
| Create links      | &check;       | &check;   | &cross; |
| Modify links      | &check;       | &check;   | &cross; |
| Remove links      | &check;       | &check;   | &cross; |
| Create calendars  | &check;       | &check;   | &cross; |
| Modify calendars  | &check;       | &check;   | &cross; |
| Remove calendars  | &check;       | &check;   | &cross; |

> **Owner role** cannot be set. The current implementation doesn't support transferring the ownership to another user.

&nbsp;
&nbsp;
## Guides 

Table of contents:

1. [Create a user](#create-user)
2. [Get users from domain](#get-users)


&nbsp;
&nbsp;
<a name="create-user"></a>
### Create a user in your domain 

To create a user in your domain you need to provide one of the user roles: `User`, `Installer` or `Administrator`, alias and password into to request body and user email. After successful response the user will receive an email with the login information.
```
curl --request PUT 'https://api.danalock.com/users/v1/{email}' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "role": "User",
    "alias": "Guest user",
    "password": "Test1234"
}'
```

&nbsp;
&nbsp;
<a name="get-users"></a>
### Get users in your domain 
To see all users that are in your domain you can use following endpoint `GET /users/v1`. If the user is created in your domain, the value `is_shadow_user` will be _false_. The shadow user is a invited user, that doesn't belong to your domain and has only permission to operate with your lock device.  

```
curl 'https://api.danalock.com/users/v1' \
--header 'Authorization: Bearer xxx'
```

Response is an array of users.
```
[
    {
       "id": "user-95932a6248fa",
       "username": "test@danalock.com",
       "display_name": "test@danalock.com",
       "role": "user",
       "email_address": "test@danalock.com",
       "is_shadow_user": false,
       "alias": "Guest user"
    }
]
```

&nbsp;
&nbsp;
>You can find more endpoints to manage user domain in the section [UsersV1](https://api.danalock.com/swagger/index.html#/UsersV1) and endpoints for managing user are in the section [UserV1](https://api.danalock.com/swagger/index.html#/UserV1).

