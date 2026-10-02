
## Overview

Table of contents:

1. [Lock operations](#lock-operations)
2. [Device information operations](#device-information-operations)
3. [Device time and timezone operations](#device-time--timezone-information)
4. [Pin codes operations](#pin-codes-operations)

&nbsp;
&nbsp;

<a name="lock-operations"></a>
## Lock operations
Lock operations can be used only for a lock device.

### Operate lock device
This operation can lock or unlock the lock device.

You have to provide one value in the arguments.

| Operation        |
|------------------|
| afi.lock.operate |

| Arguments | Index | Type   | Values           |
|-----------|-------|--------|------------------|
| operation | 0     | string | "unlock", "lock" |


```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.lock.operate",
    "arguments":["lock"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Get state
This operation returns **device state** Locked or Unlocked, **lock status** such as Locking or Unlocking, and **is blocked** status. When the result of is_blocked is `true`, that means the motor is being blocked and it prevents the movements of locking or unlocking operation.



| Operation          |
|--------------------|
| afi.lock.get-state |


```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.lock.get-state",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "state": "Locked",
        "lock_status": "Idle",
        "is_blocked": false,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result      | Type    | Values                                                   |
|-------------|---------|----------------------------------------------------------|
| state       | string  | "Locked", "Unlocked", "Unknown"                          |
| lock_status | string  | "Locking", "Unlocking", "Unknown", "Calibrating", "Idle" |
| is_blocked  | boolean | true, false                                              |


### Get lock settings

This operation returns lock device settings:
- **Speed**: The LMM supports five different motor speeds numbered 1 through 5 (5 being the fastest). The speed settings were implemented because the DanalockV1 and DanalockV2 had such a setting.
> The **speed** setting is unnecessary on the Danalock V3 since it only decreases the torque to decrease the speed, but the value is still provided.
- **Twist assist**: Allows the lock to finish lock or unlock operation on handle movement.
>  **Twist assist** was removed in the firmware version 0.23.14 (December 2023), but the value is still provided.
- **Auto lock**: Timer for the lock operation. When enabled, a timer is started when the lock-state changes from unlocked to locked. When the timer expires after `n` seconds a lock operation is performed.
- **Hold back latch**: Also known as unlocking until blocked state. When enabled the Danalock V3 will hold back the latch at the end of unlock operations and release it again after `n` seconds. Setting this to *0* disables the feature. If you set hold back latch and while auto lock is enabled, then the auto lock time value must be higher than hold back latch timeout.
- **End to end mode**: Allows to perform lock/unlock operations regardless of the known lock state. End to end can be set to mode `1` or mode `2`. **Mode 1** sets the lock-state to `locked` after a completed unlock or lock operation, regardless of known-state. **Mode 2** was introduced in the firmware version 0.20.0. Mode 2 makes lock state more reliable than in mode 1 as it adjust the calibration after every unlocking operation. If end to end mode is enabled, the lock state will be `unknown` in afi.lock.get-state operation.


| Operation             |
|-----------------------|
| afi.lock.get-settings |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.lock.get-settings",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "speed": 5,
        "twist_assist": 0,
        "auto_lock": 0,
        "hold_back_latch": 5,
        "end_to_end_mode": 0,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
| Result          | Type | Values                    |
|-----------------|------|---------------------------|
| speed           | int  | 1 - 5                     |
| twist_assist    | int  | 0 (disabled), 1 (enabled) |
| auto_lock       | int  | 0 (disabled), n (seconds) |
| hold_back_latch | int  | 0 (disabled), n (seconds) |
| end_to_end_mode | int  | 0 (disabled), 1, 2        |

### Set lock settings

This operation sets lock device settings. Detail description can be found above in Get lock settings.

- **Speed**: 1 through 5 (5 being the fastest).
- **Twist assist** was removed in the firmware version 0.23.14 (December 2023), but the value must be still provided in the arguments, you can set it to `"0"`.
- **Auto lock**: Value in seconds.  If you set hold back latch and while auto lock is enabled, then the auto lock time value must be higher than hold back latch timeout.
- **Hold back latch**: Value in seconds. If you set hold back latch and while auto lock is enabled, then the auto lock time value must be higher than hold back latch timeout.
- **End to end mode**: Can be set to `"Disabled"`, `"One"` or `"Two"`.

You have to provide 5 values in arguments.

| Operation             |
|-----------------------|
| afi.lock.set-settings |

| Arguments       | Index | Type   | Values                   |
|-----------------|-------|--------|--------------------------|
| speed           | 0     | string | "1 - 5"                  |
| twist assist    | 1     | string | "0"                      |
| auto lock       | 2     | string | "60"                     |
| hold back latch | 3     | string | "30"                     |
| end to end      | 4     | string | "Disabled", "One", "Two" |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.lock.set-settings",
    "arguments":["5", "0", "60","30", "Disabled"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

&nbsp;
&nbsp;
<a name="device-information-operations"></a>
## Device information operations

### Identify

This operation will establish connection to the lock device and light the LED.

| Operation           |
|---------------------|
| afi.device.identify |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.device.identify",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Battery level

This operation returns current battery level. When the battery level is below 30, it is said to be critical.

| Operation                         |
|-----------------------------------|
| afi.power-source.get-information2 |

```
curl https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.device.identify",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "battery_level": 89,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
| Result        | Type | Values  |
|---------------|------|---------|
| battery_level | int  | 0 - 100 |

### Device information

This operation returns information about the device: **product**, **hardware version**, **firmware version**, **firmware identifier**.

**Product:**
`"Danalock", "Bridge", "Danapad", "Universal","HttpGateway", "Ekey"`

| Operation                  |
|----------------------------|
| afi.device.get-information |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.device.get-information",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "product": "Danalock",
        "hardware_version": "4.1.4",
        "firmware_version": "0.23.14",
        "firmware_identifier": "DanalockV3_101-025_D1_0.23.14_20240221132610",
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
| Result              | Type   | Values                                         |
|---------------------|--------|------------------------------------------------|
| product             | string | "Danalock"                                     |
| hardware_version    | string | "4.1.4"                                        |
| firmware_version    | string | "0.23.14"                                      |
| firmware_identifier | string | "DanalockV3_101-025_D1_0.23.14_20240221132610" |

&nbsp;
&nbsp;
<a name="device-time"></a>
## Device time & timezone information

### Get local time

This operation returns **year**, **month**, **day**, **hour**, **minute**, **second** and **day** of the week in local time of the device.

| Operation               |
|-------------------------|
| afi.time.get-local-time |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.time.get-local-time",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "year": 2024,
        "month": 4,
        "day_of_month": 11,
        "hour": 14,
        "minute": 17,
        "second": 10,
        "day_of_week": 3,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result       | Type | Values                                                       |
|--------------|------|--------------------------------------------------------------|
| year         | int  | 1970-[2038](https://en.wikipedia.org/wiki/Year_2038_problem) |
| month        | int  | 1-12                                                         |
| day_of_month | int  | 1-31                                                         |
| hour         | int  | 0-23                                                         |
| minute       | int  | 0-59                                                         |
| second       | int  | 0-59                                                         |
| day_of_week  | int  | 1-53                                                         |

### Get utc time

This operation return a utc timestamp.

| Operation        |
|------------------|
| afi.time.get-utc |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.time.get-utc",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "utc": 1712838336,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result | Type | Values                  |
|--------|------|-------------------------|
| utc    | long | 1712838336 (unix epoch) |

### Set utc time

This operation sets a utc timestamp.

You have to provide one value in the arguments.

| Operation        |
|------------------|
| afi.time.set-utc |

| Arguments | Index | Type   | Values       |
|-----------|-------|--------|--------------|
| utc       | 0     | string | "1712838336" |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.time.set-utc",
    "arguments":["1712838336"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Set timezone transitions 

This operation sets a timezones information on the device. Timezone must be set so the lock can validate time restricted pin codes. The content of the timezone information consist of **timezone offset** *(in seconds)* and timezone transition list (**utc timestamp** and **utc offset**). DMI modules uses the timezone information and the UTC time to generate localtime.

You have to provide at least 3 arguments. If you want to provide multiple transitions add additional **utc timestamp** and **utc offset** to arguments. 

| Operation                          |
|------------------------------------|
| afi.time.set-time-zone-information |

| Arguments       | Index | Type   | Values           |
|-----------------|-------|--------|------------------|
| timezone offset | 0     | string | "3600" (seconds) |
| utc timestamp   | 1     | string | "1713177087"     |
| utc offset      | 2     | string | "3600" (seconds) |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.time.set-time-zone-information",
    "arguments":["3600","1713177087", "3600"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

&nbsp;
&nbsp;
<a name="pin-codes-operations"></a>
## Pin codes operations

### Get information

This operation returns available pin code slots and min/max length of pin code. The lock supports 20 slots, that means that you can set 20 pin codes on the lock. Each pin code has an index which is the slot where pin code is stored.

| Operation                     |
|-------------------------------|
| afi.pin-codes.get-information |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.get-information",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "supported_pin_codes": 20,
        "pin_code_length_minimum": 4,
        "pin_code_length_maximum": 11,
        "supported_time_restriction_rules": 40,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result                           | Type | Values |
|----------------------------------|------|--------|
| supported_pin_codes              | int  | 20     |
| pin_code_length_minimum          | int  | 4      |
| pin_code_length_maximum          | int  | 11     |
| supported_time_restriction_rules | int  | 40     |

### Get pin code settings

This operation returns pin codes **status**, known as *vacation mode* and **easy lock** status.
- **Status**: Known as vacation mode, can be set to `"Enabled"` or `"Disabled"` When the status is `"Disabled"` all pin codes that are set on the lock becomes disabled.
- **Easy locking status**: Can be set to `"Enabled"` or `"Disabled"` When the easy locking status is `"Enabled"` the user can lock device by pressing lock button on the Danapad without using the pin code for the lock operation.


| Operation                  |
|----------------------------|
| afi.pin-codes.get-settings |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.get-settings",
    "arguments":[]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "status": 1,
        "easy_locking_status": 1,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result              | Type | Values                                 |
|---------------------|------|----------------------------------------|
| status              | int  | 0 (unknown), 1 (enabled), 2 (disabled) |
| easy_locking_status | int  | 0 (unknown), 1 (enabled), 2 (disabled) |

### Set pin code settings

This operation sets pin codes status (*vacation mode*) and easy lock status.
- **Status**: Known as vacation mode, can be set to `"Enabled"` or `"Disabled"` When the status is `"Disabled"` all pin codes that are set on the lock becomes disabled.
- **Easy locking status**: Can be set to `"Enabled"` or `"Disabled"` When the easy locking status is `"Enabled"` the user can lock device by pressing lock button on the Danapad without using the pin code for the lock operation.

You have to provide 2 values in the arguments.

| Operation                  |
|----------------------------|
| afi.pin-codes.set-settings |

| Arguments           | Index | Type   | Values                |
|---------------------|-------|--------|-----------------------|
| status              | 0     | string | "Enabled", "Disabled" |
| easy_locking_status | 1     | string | "Enabled", "Disabled" |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.set-settings",
    "arguments":["Enabled","Enabled"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
> ALTERNATIVE OPERATION
>
> *Note: Unintuitive implementations like this exists for backwards compatible reasons*

Each of these two settings can be set individually by specifying the name and status. In this case, **status** can be set to `"1"` (Enabled) or `"2"` (Disabled).

| Operation                 |
|---------------------------|
| afi.pin-codes.set-setting |

| Arguments | Index | Type   | Values                        |
|-----------|-------|--------|-------------------------------|
| setting   | 0     | string | "Status", "EasyLockingStatus" |
| status    | 1     | string | "1" (enabled), "2" (disabled) |
```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.set-settings",
    "arguments":["EasyLockingStatus,"2"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Get pin codes

This operation returns list of pin codes. You need to specify a range how many pin codes you want to retrieve. In this example, only one pin code from the first slot is retrieved.

You have to provide one value in the arguments.

| Operation                   |
|-----------------------------|
| afi.pin-codes.get-pin-codes |

| Arguments   | Index | Type   | Values |
|-------------|-------|--------|--------|
| slots range | 0     | string | "1-20" |


```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.get-pin-codes",
    "arguments":["1"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "pin_codes": [
            {
                "identifier": 1,
                "status": 2,
                "digits": "123456"
            }
        ],
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result     | Type   | Values                                              |
|------------|--------|-----------------------------------------------------|
| pin_codes  | array  |                                                     |
| identifier | int    | 1 - 20                                              |
| status     | int    | 0 (unknown), 1 (cleared), 2 (enabled), 3 (disabled) |
| digits     | string | "123456"                                            |

### Get pin code

This operation returns pin code based on specified identifier.

You have to provide one value in the arguments.

| Operation                  |
|----------------------------|
| afi.pin-codes.get-pin-code |

| Arguments  | Index | Type   | Values |
|------------|-------|--------|--------|
| identifier | 1     | string | "1-20" |


```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.get-pin-code",
    "arguments":["1"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "pin_code": {
            "identifier": 1,
            "status": 2,
            "digits": "123456"
        },
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result     | Type   | Values                                              |
|------------|--------|-----------------------------------------------------|
| pin_code   | object |                                                     |
| identifier | int    | 1 - 20                                              |
| status     | int    | 0 (unknown), 1 (cleared), 2 (enabled), 3 (disabled) |
| digits     | string | "123456"                                            |

### Set pin code

This operation sets a pin code. Pin code must have an identifier that is dependent on slot where the pin code is stored, status and digits.

You can set maximum 20 pin codes on the lock device.

**Pin code status:**
- **Cleared**: Pin code slot is available for creating a new pin code.
- **Enabled**: Pin code is set and can be used for lock/unlock operation.
- **Disabled**: Pin code is set but cannot be used by user.

You have to provide 3 values in the arguments.

| Operation                  |
|----------------------------|
| afi.pin-codes.set-pin-code |

| Arguments  | Index | Type   | Values                           |
|------------|-------|--------|----------------------------------|
| identifier | 0     | string | "1-20"                           |
| status     | 1     | string | "Cleared", "Enabled", "Disabled" |
| digits     | 2     | string | "123456"                         |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.set-pin-code",
    "arguments":["1","Enabled","123456"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Set pin codes

This operation allows you to set multiple pin codes. In this example, two pin codes are created at the same time.

The argument array needs to contain a number of values that is a multiple of three, each 3-tuple in the order **identifier**, **status** and **digits**.

You have to provide at least one such 3-tuple (and at most 20) in the argument list.

| Operation                   |
|-----------------------------|
| afi.pin-codes.set-pin-codes |

| Arguments  | Index | Type   | Values                           |
|------------|-------|--------|----------------------------------|
| identifier | 0     | string | "1-20"                           |
| status     | 1     | string | "Cleared", "Enabled", "Disabled" |
| digits     | 2     | string | "123456"                         |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.set-pin-codes",
    "arguments":["1","Enabled","123456","2","Enabled","654321"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Get time restriction rules

This operation returns time restriction interval for a pin code. You have to specify the pin code **identifier** in the arguments. There are two types of pin codes that can be set. A temporal pin code period from and to a specific datetime. And a recurring, for example time period every Monday from 8:00 till 16:00. The pin code type is not restricted, both can be set at the same time.

You have to provide one value in the arguments.

| Operation                                |
|------------------------------------------|
| afi.pin-codes.get-time-restriction-rules |

| Arguments  | Index | Type   | Values                                      |
|------------|-------|--------|---------------------------------------------|
| identifier | 0     | string | "1-20"                                      |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.get-time-restriction-rules",
    "arguments":["1"]
}'
```
>Recurring time period every Monday from 8:00 till 16:00
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "rules": [
            {
                "handle": 1,
                "from": {
                    "date_time": {
                        "year": 0,
                        "month": 0,
                        "day_of_month": 0,
                        "hour": 0,
                        "minute": 0
                    },
                    "weekday": 1,
                    "hour": 8,
                    "minute": 0
                },
                "to": {
                    "date_time": {
                        "year": 0,
                        "month": 0,
                        "day_of_month": 0,
                        "hour": 0,
                        "minute": 0
                    },
                    "weekday": 0,
                    "hour": 16,
                    "minute": 0
                }
            }
        ],
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
>Temporal pin code with time period from 12.4.2024 08:00 till 13.4.2024 16:00
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "rules": [
            {
                "handle": 2,
                "from": {
                    "date_time": {
                        "year": 2024,
                        "month": 4,
                        "day_of_month": 12,
                        "hour": 8,
                        "minute": 0
                    },
                    "weekday": 0,
                    "hour": 0,
                    "minute": 0
                },
                "to": {
                    "date_time": {
                        "year": 2024,
                        "month": 4,
                        "day_of_month": 13,
                        "hour": 16,
                        "minute": 0
                    },
                    "weekday": 0,
                    "hour": 0,
                    "minute": 0
                }
            }
        ],
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

| Result       | Type   | Values                                                       |
|--------------|--------|--------------------------------------------------------------|
| rules        | array  |                                                              |
| handle       | int    | 1 - 40                                                       |
| from         | object |                                                              |
| to           | object |                                                              |
| date_time    | object |                                                              |
| year         | int    | 1970-[2038](https://en.wikipedia.org/wiki/Year_2038_problem) |
| month        | int    | 1-12                                                         |
| day_of_month | int    | 1-31                                                         |
| hour         | int    | 0-23                                                         |
| minute       | int    | 0-59                                                         |
| weekday      | int    | 1-53                                                         |

### Set time restriction

This operation sets time restriction interval for a pin code. There are two types of pin codes that can be set: recurring and temporal. Both can be set at the same time.

- **Recurring pin code**: A time period that starts on specified days from given hour till given hour. 
- **Temporal pin code**: A period from specified date and time to specified date and time. You can set weekday to `"DISABLED"` in arguments, if you don't want to set recurring time period. 

You have to provide 3 values in the arguments. 

| Operation                                  |
|--------------------------------------------|
| afi.pin-codes.create-time-restriction-rule |

| Arguments  | Index | Type   | Values                                          |
|------------|-------|--------|-------------------------------------------------|
| identifier | 0     | string | "1-20"                                          |
| valid from | 1     | string | "1","0 0 0 00:00 1 08:00","0 0 0 00:00 0 16:00" |
| valid to   | 2     | string | "1","0 0 0 00:00 1 08:00","0 0 0 00:00 0 16:00" |

>Recurring time period every Monday from 8:00 till 16:00
```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.create-time-restriction-rule",
    "arguments":["1","0 0 0 00:00 1 08:00","0 0 0 00:00 0 16:00"]
}'
```
>Temporal pin code with time period from 12.4.2024 08:00 till 13.4.2024 16:00
```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.create-time-restriction-rule",
    "arguments":["1","2024 04 12 08:00 DISABLED 00:00","2024 04 13 16:00 DISABLED 00:00"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "handle": 1,
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```

### Delete time restriction

This operation removes time restriction interval from a pin code.

You have to provide one value in the arguments.

| Operation                                  |
|--------------------------------------------|
| afi.pin-codes.delete-time-restriction-rule |

| Arguments                      | Index | Type   | Values |
|--------------------------------|-------|--------|--------|
| (time restriction rule) handle | 0     | string | "1-40" |

```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.pin-codes.delete-time-restriction-rule",
    "arguments":["1"]
}'
```
Response
```
{
    "id": "a37126cc-0537-4f0b-a6c7-588eeb30a185",
    "status": "Succeeded",
    "result": {
        "afi_status": 0,
        "afi_status_text": "Ok",
        "dmi_status": 0,
        "dmi_status_text": "Ok"
    }
}
```
