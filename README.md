

## Overview

### Introduction

The Bridge Service is used to execute AFI operations on devices remotely. To reach the device via the Internet, the device needs to be "paired" with a WiFi Danalock **bridge device**.

The service has two end points:

- `POST /bridge/v1/execute` For connecting to the device and starting AFI operations.
- `POST /bridge/v1/poll` For querying for the completion of previously started operations.


The endpoints accept and return payloads in JSON format.


Both endpoints require a valid access-token obtained from the DanaServer. This access token must be for a user with permissions to execute the requested operation, otherwise the service will return an error when trying to perform the operation.

&nbsp;
&nbsp;
### API Request

**POST /bridge/v1/execute**

Operations that can be executed using the bridge service are a subset of the AFI operations supported by the actual device. The parameter `arguments` is always an array of strings *(except for batch operations)*. The following example is executing the operation `afi.lock.operate`.

```
POST /bridge/v1/execute
Content-Type: application/json
Authorization: Bearer <base64 access-token>

{
  "device": "11:22:33:44:55:66",
  "operation": "afi.lock.operate",
  "arguments": ["lock"]
}

```
Response
```
{
  id": "a7c464f3-c78d-4cc1-9af3-be89185a3274"
}
```
Curl example
```
curl 'https://api.danalock.com/bridge/v1/execute' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "device":"11:22:33:44:55:66",
    "operation":"afi.lock.get-state",
    "arguments":["lock"]
}'
```
&nbsp;
&nbsp;

**POST /bridge/v1/poll**

The purpose of this endpoint is polling for the completion of previously started operations. If the operation status is in the progress, the result will be empty. Result always contains of AFI and DMI statuses and additional parameters based on the operation type. An in-flight operation will time out after 60s.



```
POST /bridge/v1/poll
Content-Type: application/json
Authorization: Bearer <base64 access-token>

{
 "id": "a7c464f3-c78d-4cc1-9af3-be89185a3274"
}
```
Response of an operation in progress
```
{
  "id": "a7c464f3-c78d-4cc1-9af3-be89185a3274",
  "status": "InProgress",
  "result": null
}
```
Response of an operation completed successfully
```
{
  "id": "a7c464f3-c78d-4cc1-9af3-be89185a3274",
  "status": "Succeeded",
  "result": {
      "afi_status": 0
      "afi_status_text": "Ok"
      "dmi_status": 0
      "dmi_status_text": "Ok"
  }
}
```
Response of an failed operation 
```
{
    "id": "abd054e0-eea7-4645-9333-fd1d3151e191",
    "status": "Failed",
    "result": {
        "bridge_server_status": 4,
        "bridge_server_status_text": "BridgeBusy"
    }
}
```

**Bridge server statuses:**
- Ok
- Error
- JobTimeout
- BridgeNotAttached *(In some cases, the response status 404 is returned, when operation is called on non-bridged device)*
- BridgeBusy
- UnableToConnectToDevice


Curl example
```
curl 'https://api.danalock.com/bridge/v1/poll' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer xxx' \
--data '{
    "id":"a7c464f3-c78d-4cc1-9af3-be89185a3274"
}'
```
&nbsp;
&nbsp;

### Gotchas

The result of the operation depends on the afi operation that was executed. Some operations such as **afi.lock.set-settings** and **afi.lock.get-settings** are using enum values. When you execute **afi.lock.set-settings** you have to provide some of the enum values as a string value. For example, in the case of end to end mode, you can provide following values `"Disabled"`, `"One"` or `"Two"`. However, the **afi.lock.get-settings** operation will return enums values as integers. `0` for "Disabled", `1` for mode "One" and `2` for mode "Two". This pattern applies for all get/set operations that are using enums with `"Disabled"`, `"Enabled"` values.
> *Note: This unintuitive type discrepancy is a legacy implementation and exists for backwards compatible reasons*
