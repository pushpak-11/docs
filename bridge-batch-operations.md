

## Overview


### Introduction

Executing multiple operations over the Danabridge can be slow. This is because each command needs to establish a Bluetooth BLE connection from the bridge to the device (lock).

This can be optimized if no reasoning needs to be done by the client between operations by sending the commands in a **batch operation**. The batch operation will proceed to execute the list until one of the commands fails by returning anything else than `"afi_status" = 0` and `"dmi_status" = 0.`

Request body example
```
POST /bridge/v1/execute

{
    "device" : "de:ad:be:ef:00:00",
    "operation" : "batch",
    "arguments" : [
        {
        "operation" : "afi.power-source.get-information2"
        },
        {
        "operation" : "afi.lock.get-state"
        },
    ]
}
```

&nbsp;
&nbsp;

### Batch operation example

The batch arguments are a series of AFI operations. The individual operations in the batch arguments have 3 fields:

- **operation** : The usual [AFI operation](https://github.com/Danalock/BackendDocs/blob/main/bridge-service/bridge-operations.md) you know and love.
- **arguments** : The usual arguments to said operation
- **accept** : A pattern which will be applied to the result of the operation (DMI-status/AFI-status). If the pattern matches, the specific operation will be regarded as succeeded and the batch processing will NOT stop.

The “accept” field facilitates ignoring non-zero return from operations, since some AFI operations can return non-zero status in situation where nothing has conceptually failed. Bear in mind that the value is a pattern. So if, say, afi_status is not mentioned, it is a wildcard, accepting any AFI return value. In other words, to completely ignore any return value and continue the batch regardless, state the pattern : {}

```
POST /bridge/v1/execute

{
    "device" : "de:ad:be:ef:00:00",
    "operation" : "batch",
    "arguments" : [
      {
        "operation" : "afi.power-source.get-information2"
      },
      {
        "operation" : "afi.lock.operate",
        "arguments": ["lock"],
        "accept" : [
           {
          "afi_status" : 7,
          "dmi_status" : 18
           }
        ]
      },
      {
       "operation" : "afi.time.get-utc"
      }
    ]
}
```

The resulting status of the complete batch command will be `Success` if all steps were executed. Otherwise, `Failed`.

In other words, a batch might have all steps executed and return `Success`, even if the last command returned an AFI failure. You can’t avoid checking the result of individual commands.

The **batch_results** is an array in the same order as requested operations.

An example response body:

```
{
    "id": "d394df41-9d5c-4cdd-8041-c5e4e60fc5c3",
    "status": "Succeeded",
    "result": {
      "batch_results": [
        {
          "operation": "afi.power-source.get-information2",
          "result": {
            "battery_level": 85,
            "afi_status": 0,
            "afi_status_text": "Ok",
            "dmi_status": 0,
            "dmi_status_text": "Ok"
          }
        },
        {
          "operation": "afi.lock.operate",
          "result": {
            "afi_status": 7,
            "afi_status_text": "NothingChanged",
            "dmi_status": 18,
            "dmi_status_text": "ApplicationLayerProtocolError"
          }
        },
        {
          "operation": "afi.time.get-utc",
          "result": {
            "utc": 1709299999,
            "afi_status": 0,
            "afi_status_text": "Ok",
            "dmi_status": 0,
            "dmi_status_text": "Ok"
          }
        }
      ]
    }
}
```
