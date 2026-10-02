## List of bridge operations 

This is an overview of all bridge operations with a brief description. 

&nbsp;
&nbsp;

Device information

| Operation                                                                                                                         | Description                                                      |
|-----------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [afi.device.identify](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#identify)                    | establish connection to a device and light the LED               |
| [afi.power-source.get-information2](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#battery-level) | battery level                                                    |
| [afi.device.get-information](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#device-information)   | product, hardware version, firmware version, firmware identifier |


&nbsp;
&nbsp;

Device timezone information

| Operation                                                                                                                         | Description                                             |
|-----------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| [afi.time.set-utc](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-utc-time)                   | set utc unix timestamp                                  |
| [afi.time.get-utc](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-utc-time)                   | utc timestamp                                           |
| [afi.time.get-local-time](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-local-time)          | year, month, day, hour, minute and second in local time |
| [afi.time.set-time-zone-information](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-timezone) | set timezone offset and rules                           |

&nbsp;
&nbsp;

Lock operations

| Operation                                                                                                                 | Description                                                          |
|---------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [afi.lock.get-state](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-state)            | device current lock state                                            |
| [afi.lock.operate](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#operate-lock-device)    | lock or unlock a device                                              |
| [afi.lock.get-settings](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-lock-settings) | speed, twist assist, auto lock, hold back latch, end to end mode     |
| [afi.lock.set-settings](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-lock-settings) | set speed, twist assist, auto lock, hold back latch, end to end mode |

&nbsp;
&nbsp;

 Pincodes

| Operation                                                                                                                                             | Description                                 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| [afi.pin-codes.get-information](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-information)                       | pin code slots, pin code max and min length |
| [afi.pin-codes.set-settings](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-pin-code-settings)                    | set vacation mode, easy lock                |
| [afi.pin-codes.set-setting](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-pin-code-settings)                     | set vacation mode or easy lock              |
| [afi.pin-codes.get-settings](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-pin-code-settings)                    | vacation mode, easy lock                    |
| [afi.pin-codes.set-pin-code](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-pin-code)                             | set pin code                                |
| [afi.pin-codes.set-pin-codes](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-pin-codes)                           | set multiple pin codes                      |
| [afi.pin-codes.get-pin-code](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-pin-code)                             | get pin code, index, status                 |
| [afi.pin-codes.get-pin-codes](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-pin-codes)                           | get all pin codes                           |
| [afi.pin-codes.get-time-restriction-rules](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#get-time-restriction-rules) | get time restriction of the pin code        |
| [afi.pin-codes.create-time-restriction-rule](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#set-time-restriction)     | set time restriction for the pin code       |
| [afi.pin-codes.delete-time-restriction-rule](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-api.md#delete-time-restriction)  | delete time restriction of the pin code     |





