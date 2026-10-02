## Device Management

### Introduction

In the domain there is a difference between **locks** and other **afi devices**. The afi device such as DanaBridge or DanaPad is an auxiliary device that can be paired to a lock and belongs to only one domain. You cannot give a permission to other user to manage DanaBridge or Danapad.
The lock also belongs to only one domain, but the access can be shared with other users. 

&nbsp;
&nbsp;
### Lock device

You can give a permission to other user to operate with a lock in your domain by creating a link and a link profile, that determines what permission the user has. 

>Invited user that has a permission to use your lock device doesn't belong to your domain and will be marked as **shadow user**.

For creating a link between the device and user you need:
- **[Required]** Device that should be granted access to. This is either a `lockId` or `groupId`. 
- **[Required]** Entity that should be granted access to. This is either a `userId` or `groupId`. 
- **[Optional]** When is the access valid. This is a `calendarId`. If none is given, access will be permanent. 
- **[Optional]** What permissions should the user have to the device. This is a link profile name (Administrator, Installer or User). If none is given, the `User` role will be used as default. For groups, `User` profile is the default.

**Link profile** determine what permissions a user has when being granted access to a lock.

| Action              | Administrator | Installer | User    | User with remote access |
|---------------------|---------------|-----------|---------|-------------------------|
| Enroll devices      | &check;       | &check;   | &cross; | &cross;                 |
| Disenroll devices   | &check;       | &cross;   | &cross; | &cross;                 |
| Set timezone        | &check;       | &check;   | &cross; | &cross;                 |
| Lock/ unlock        | &check;       | &cross;   | &check; | &check;                 |
| Remote Lock/ unlock | &check;       | &cross;   | &cross; | &check;                 |
| Get details         | &check;       | &cross;   | &check; | &check;                 |
| Set pin codes       | &check;       | &cross;   | &cross; | &cross;                 |
| Pair devices        | &check;       | &cross;   | &cross; | &cross;                 |

&nbsp;
&nbsp;
### Groups

The user locks can be managed by groups. You can create a **groups of locks** or **groups of users**. As it is a collection of items, you can also create groups of groups for an advanced configurations.
Groups are used to grand a user or a group of users access to a lock or a group of locks.

See swagger documentation section [GroupsV1](https://api.danalock.com/swagger/index.html#/GroupsV1).

&nbsp;
&nbsp;
### Activity log
Every user interaction with the lock device is logged. You can retrieve information about user and locks activities.

See swagger documentation section [LogV1](https://api.danalock.com/swagger/index.html#/LogV1).

&nbsp;
&nbsp;
## Guides

Table of contents:

1. [Get locks](#get-locks)
2. [Get all devices from user domain](#get-devices)
3. [Give user permission to operate with lock](#sent-invitation)
4. [Create links](#links)
5. [Pair lock with a bridge](#pair-device)
6. [Create a group](#groups)

&nbsp;
&nbsp;
<a name="get-locks"></a>
### Get locks

To get all locks you have access to from all domains you can use the endpoint `GET /ekey/v3/users/{email}/keys`. This endpoint returns an array of `keys` and a `refresh period` in seconds. The key object stores every information about the lock device such as: user information, device information, firmware information, permissions, login tokens for operating with the device and list of paired devices. 
Refresh period determines the validity of a key and how often it has to be refreshed.


There is no link profile information, but there is a permission list. There are maximum 13 permissions that can have only Administrator. The default User role (guest) would have 2 permissions in the list with an id 3 and 10.


&nbsp;
>**USE CASE:** The endpoint [GET /ekey/v3/users/{email}/keys](https://api.danalock.com/swagger/index.html#/EkeyV3/GetKeys) is suitable for mobile applications when you need all locks and keys that you can store on mobile device for offline usage to establish connection to a lock and decrypt broadcast data.

&nbsp;
```
curl --request POST 'https://api.danalock.com/ekey/v3/users/keys' \
--header 'Authorization: Bearer xxx'
```
Response
```
{
    "keys": [
        {
            "user": {
                "user_id": "user-e093f34948c0",
                "token_id": "0",
                "can_remote": 0,
                "can_delete": 1,
                "can_share": 1,
                "external": {
                    "id": "0",
                    "source": "0"
                }
            },
            "serial_number": "9a:8b:7c...",
            "name": "Lock device",
            "timezone": "Europe/Zurich",
            "valid_from": 0,
            "valid_to": 1721906465,
            "time_restrictions": [],
            "permissions": [
                {
                    "id": "1",
                    "description": ""
                },
                {
                    "id": "2",
                    "description": ""
                },
                {
                    "id": "3",
                    "description": ""
                },
                {
                    "id": "4",
                    "description": ""
                },
                {
                    "id": "5",
                    "description": ""
                },
                {
                    "id": "6",
                    "description": ""
                },
                {
                    "id": "7",
                    "description": ""
                },
                {
                    "id": "8",
                    "description": ""
                },
                {
                    "id": "9",
                    "description": ""
                },
                {
                    "id": "10",
                    "description": ""
                },
                {
                    "id": "11",
                    "description": ""
                },
                {
                    "id": "12",
                    "description": ""
                },
                {
                    "id": "13",
                    "description": ""
                }
            ],
            "login_token": "AQImB...",
            "advertising_key": "5835D...",
            "data": null,
            "product": {
                "firmware": {
                    "firmware_version": "0.20.0",
                    "variation": "",
                    "pcb_version": "101-025_D1",
                    "updates": false
                },
                "name": "danalockv3",
                "type": "danalock",
                "version": "v3"
            },
            "paired": [
                {
                    "parent": {
                        "serial_number": "4c:5b:69...",
                        "type": "danabridgev3"
                    },
                    "child": {
                        "serial_number": "9a:8b:7c...",
                        "type": "danalockv3"
                    }
                }
            ]
        }
    ],
    "refresh": 86400
}
```

&nbsp;
&nbsp;
<a name="get-devices"></a>
### Get all devices from user domain

There are multiple endpoints for getting user devices. If you need a list of afi devices with basic information such as serial number, name, device type and timezone you can use endpoint `/devices/v1`. This endpoint returns all afi devices and locks in your domain. If you want to retrieve only locks that are within your domain you can use `/devices/v1/linked`. When you need to establish a connection to a device and see list of permission use `POST /devices/v1/login_tokens` endpoint.
See the section [DevicesV1](https://api.danalock.com/swagger/index.html#/DevicesV1) from the swagger documentation.

&nbsp;
>There is a difference between `POST /devices/v1/login_tokens` and `POST /devices/v1/{serialNumber}/login_token`! You can set expiration time only when you are creating afi login token for all devices. In case of `POST /devices/v1/{serialNumber}/login_token` request, the expiration period will be kept to 24h,  regardless of period you set in the request body. 

&nbsp;
>**USE CASE:** This endpoint is suitable for web applications that doesn't need to establish connection to a device.

&nbsp;
```
curl --location 'https://api.danalock.com/devices/v1' \
--header 'Authorization: Bearer xxx'
```
Response
```
[
    {
        "serial_number": "86:9d:33...",
        "name": "Danalock",
        "device_type": "danalockv3",
        "timezone": "Europe/Copenhagen"
    },
    {
        "serial_number": "4f:55:bc...",
        "name": "Danabridge",
        "device_type": "danabridgev3",
        "timezone": "Europe/Copenhagen"
    },
    {
        "serial_number": "87:2d:9e...",
        "name": "Danapad",
        "device_type": "danapadv3",
        "timezone": "Europe/Copenhagen"
    }
]
```
&nbsp;
>**Timezone** in this response is just server side information, if the application is not updating the timezone information it can be null or outdated. To update server timezone you can use endpoint [PATCH devices/v1/{serialNumber}](https://api.danalock.com/swagger/index.html#/DevicesV1/UpdateDeviceBySerialNumber).

&nbsp;
&nbsp;
<a name="sent-invitation"></a>
### Give user permission to operate with lock

By sending an invitation you can grant access to other user to use your lock device. If the user is already invited to your domain and you need to give them access to another lock, you need to create a link between a lock and invited user. See next section [links](#links).

&nbsp;
**Access hierarchy**

The system allows that multiple accesses can be granted for the same user and lock. If this happens, note that only one access takes effect.

&nbsp;
For these cases, there are 4 priorities, ordered by rank:

- 1: Direct access between user and lock.
- 2: Access between user and group of locks.
- 3: Access between group of users and lock.
- 4: Access between group of users and group of locks.

You can send an invitation to a user by **username**, by **token** or by **email**. Invitation with permanent access doesn't have rules attached to them, start time is specified with no end time.

&nbsp;
> **Note** that by sending an invitation you grant a default **User** role to the user.

&nbsp;
&nbsp;
**Invitation with a permanent access**
```
curl 'https://api.danalock.com/invitations/v1/invite_user' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data-raw '{
  "username": "test",
  "links": [
    {
      "lock_id": "lock-9268e...",
      "calendar_id": null
    }
  ],
  "alias": "invited via username"
}'
```

If you don't want to give user a permanent access, you can create a calendar object for an invitation with temporary or recurrent access.

**Temporary access**
- No rules attached to them.
- Start time and end time is specified.

_This example creates a calendar with temporary access from 26 of July 2024 at 8:00 till 25 of August 2024 8:00._ 

```
curl 'https://api.danalock.com/links/v1/calendars' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
  "start_time": "2024-07-26T08:00:00.000Z",
  "refresh_interval": 172800,
  "rule_sets": [
    {
      "end_time": "2024-08-25T08:00:00.000Z"
    }
  ]
}
'
```

**Recurrent access**
- Rules attached to them.
- Can be both valid within a start and end time, or forever.

_This example creates a calendar with recurring access every Saturday and Sunday from 8:00 till 16:00._

```
curl 'https://api.danalock.com/links/v1/calendars' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
  "start_time": "2024-07-26T07:58:37.157Z",
  "refresh_interval": 172800,
  "rule_sets": [
    {
      "week_days": [
        5,6
      ],
      "daily_start": 28800,
      "daily_end": 57600
    }
  ]
}
'
```
The response of successfully created calendar is a `calendarId` that you can attach to links in invitation request body.
```
{
    "id": "calendar-4b4d40302da1"
}
```

&nbsp;
>**Note:** If you want to get a list of lock ids with links to their calendars you can use endpoint `GET /users/v1/{userId}/effective_links`.

&nbsp;
&nbsp;
**Change user role**

When user accepts your invitation you can change their role to `Administrator`, `Installer` or `User`. 
```
curl --request PATCH 'https://api.danalock.com/users/v1/{userId}' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
  "role": "Administrator"
}'
```
The User role doesn't have access to remote operations by default. To grant them permission to use **remote operations** you have to update their link profile.
```
curl --request PATCH 'https://api.danalock.com/links/v1/{lockUserId}' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
  "link_profile": "user_allow_remote"
}'
```

&nbsp; 
> Note: To get **user id** you can use endpoint `GET /users/v1`. To get **lock user id** you can use endpoint `GET locks/v1` or `GET /users/v1/{userId}/effective_links`.

&nbsp;
&nbsp;
**Accepting the invitations**


You can get a list of invitations from the endpoint `GET /invitations/v1/received`. Every invitation will have token in the response body that you can use for accepting or declining the invitation. For more information see the swagger documentation, section [**InvitationsV1**](https://api.danalock.com/swagger/index.html#/InvitationsV1).

&nbsp;
&nbsp;
<a name="links"></a>
### Create a link

If you already invited user and you need to give them access to another lock from your domain, you have to create a link between lock and user. You can also attach a calendar to control access. See more in the [LinksV1](https://api.danalock.com/swagger/index.html#/LinksV1) section of the swagger documentation.

```
curl --request POST 'https://api.danalock.com/links/v1' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "lock_id": "lock-9268ef90a02b",
    "user_id": "user-ce3239da4b3f",
    "link_profile": "administrator",
    "calendar_id": null,
}'
```
>**Note:** You cannot delete a link with a calendar. To see available link_profiles use endpoint `GET domain/v1/link_profiles`

&nbsp;
&nbsp;
<a name="pair-device"></a>
### Pair lock with a bridge

The pairing between the **lock** and the **DanaBridge** afi device is a server side pairing. However, pairing between lock and DanaPad is device level pairing, fot which you will have to use SDK _(see SDK documentation for more information)_. 

To pair a lock with a DanaBridge you have to provide lock's serial number to the url query parameter and bridge serial number to the request body. 
```
curl --request POST 'https://api.danalock.com/devices/v1/86:9d:33.../paired_devices' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
  "paired_device_serial_number": "4f:55:bc..."
}'
```
You can find endpoints to get paired device and delete paired device in the section **[DevicesV1](https://api.danalock.com/swagger/index.html#/DevicesV1)** of the swagger documentation.



&nbsp;
&nbsp;
<a name="groups"></a>
### Create a group

There are two types of groups you can create: `locks` or `users`. Before you start adding locks or users to the group you have to create one, by calling endpoint `POST /groups/v1`.

```
curl --request POST 'https://api.danalock.com/groups/v1' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "type":"users",
    "name":"testers"
}'
```
This endpoint creates a group of user type and returns group id in the response with provided user data. 
```
{
    "id": "group-fba34e305e55",
    "name": "testers",
    "type": "users"
}
```
To add users to this group you have use endpoint `POST groups/v1/{groupId}/users/{userId}`.

```
curl -request POST 'https://api.danalock.com/groups/v1/group-fba34e305e55/users/user-d8131cfe981d' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx'
```
You can update and delete groups, see more in the sections [GroupsV1](https://api.danalock.com/swagger/index.html#/GroupsV1) of the swagger documentation. 

**Advanced configuration**

If you want to connect groups you can do so by creating a link between groups. However, you can create a link only between different types of group. You can also attach calendar and link profile to change access to the group. 
```
curl -request POST 'https://api.danalock.com/links/v1' \
--header 'Authorization: Bearer xxx' \
--data '{
    "group_id": "group-c42c6c262a8d",
    "other_group_id": "group-e547506ecce6"
}'
```
