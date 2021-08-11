## FIDO Device Onboard (FDO) Client SDK API Reference
This section describes the APIs provided by the FDO Client SDK as well as public data structures required to support them.

## Data Structures
These structures are defined in the FDO Client SDK header file and used by various SDK APIs.

### Generic API Return Status
This type is returned by all APIs and indicates whether the API call succeeded or failed.

_**Syntax**_
```
typedef enum {
    FDO_SUCCESS = 0,
    FDO_INVALID_PATH,
    FDO_CONFIG_NOT_FOUND,
    FDO_INVALID_STATE,
    FDO_RESALE_NOT_SUPPORTED,
    FDO_RESALE_NOT_READY,
    FDO_WARNING,
    FDO_ERROR,
    FDO_ABORT
} fdoSdkStatus;
```
_**Members**_

| | |
|-|-|
| **FDO_SUCCESS** | The API call succeeded. |
| **FDO_INVALID_PATH** | A path or address specified was not found or not accessible. |
| **FDO_CONFIG_NOT_FOUND** | An expected configuration file was not found or not accessible. |
| **FDO_INVALID_STATE** | The SDK is in an invalid state to perform the requested operation. |
| **FDO_RESALE_NOT_SUPPORTED** | This configuration of the SDK does not support the Resale Protocol. |
| **FDO_RESALE_NOT_READY** | The SDK is not in a state to execute the Resale Protocol. This error will occur when the Resale API (fdoSdkResale) is called when device ownership transfer has not yet completed successfully. |
| **FDO_WARNING** | This value is used in the SDK error callback to notify the Application that a transient failure occurred. See Error Handling Callback for details. |
| **FDO_ERROR** | The API call did not succeed. We might extend this later (TBD) to allow returning of specific error types as positive values (for example, memory allocation failure, communications failure, and so on). |
| **FDO_ABORT** | This value is returned by the error callback function to prevent the SDK from continuing the transfer of ownership protocol if an error occurred. Details are provided in Error Handling Callback. |

### FDO Device Status
This type indicates the current FDO protocol status of the device.

_**Syntax**_
```
typedef enum {
    FDO_STATE_PRE_DI = 2,
    FDO_STATE_PRE_TO1,
    FDO_STATE_IDLE,
    FDO_STATE_RESALE,
    FDO_STATE_ERROR
} fdoSdkDeviceStatus;
```
_**Members**_

| | |
|-|-|
| **FDO_STATE_PRE_DI** | The SDK is in the pre-Device Initialization state. It is ready to run the DI protocol which can be initiated by calling the `fdoSdkRun()` API. |
| **FDO_STATE_PRE_TO1** | The SDK has completed the DI stage and is ready for Device onboarding. The SDK will run the TO1 protocol if the `fdoSdkRun()` API is called. |
| **FDO_STATE_IDLE** | The SDK has completed ownership transfer and is in the idle state. Calling `fdoSdkRun()` will have no effect. The Application may only call the `fdoSdkResale()` API to initiate the Resale protocol at his point. |
| **FDO_STATE_RESALE** | The SDK has is now ready for resale. Calling `fdoSdkRun()` will run the TO1 & TO2 protocols, to carryout onboarding to the new (post-resale) device owner. |
| **FDO_STATE_ERROR** | This API failed due to an internal error. |

### FDO SDK Error Values
This type is passed from the SDK to the Application when an error occurs and indicates details of the error. It is used by the error callback – see Error Handling Callback.

_**Syntax**_
```
typedef enum {
    FDO_RV_TIMEOUT = 1,
    FDO_CONN_TIMEOUT,
    FDO_DI_ERROR,
    FDO_TO1_ERROR,
    FDO_TO2_ERROR
} fdoSdkError;
```
_**Members**_

| | |
|-|-|
| **FDO_RV_TIMEOUT** | A timeout occurred when trying to contact the Rendezvous Server. |
| **FDO_CONN_TIMEOUT** | A connection to either the Rendezvous or Owner Server timed out. |
| **FDO_DI_ERROR** | A generic error occurred during the DI stage. |
| **FDO_TO1_ERROR** | A generic error occurred during the TO1 protocol stage. |
| **FDO_TO2_ERROR** | A generic error occurred during the TO2 protocol stage. |

### FDO Service Callback Type
This type value indicates the type of function the module callback must perform. These values are used by the ServiceInfo Module Callback function.

_**Syntax**_
```
typedef enum {
    FDO_SI_START,
    FDO_SI_GET_DSI,
    FDO_SI_SET_OSI,
    FDO_SI_END,
    FDO_SI_FAILURE
} fdo_sdk_si_type;
```
_**Members**_

Below are the parameters of the ServiceInfo Module Callback function described in ServiceInfo Module Callback function.

| | |
|-|-|
| **FDO_SI_START** | This value indicates that the SDK is starting ServiceInfo rounds. The module may perform pre-preparation operations at this time. The count and si parameters will be NULL. |
| **FDO_SI_GET_DSI** | This is a legacy value that shall be removed in a future release. |
| **FDO_SI_SET_OSI** | This value indicates that the SDK is providing a valid Owner ServiceInfo key-value pair to the module and it must process the provide Owner ServiceInfo information. The count parameter is a progressively increasing index value of the provided Owner ServiceInfo. |
| **FDO_SI_END** | This value indicates that the SDK has completed all ServiceInfo rounds and the module can perform cleanup or final operations if required (like saving a file to disk, and so on). |
| **FDO_SI_FAILURE** | This value indicates that an error occurred and the SDK is aborting or abandoning this ServiceInfo round. The module must ignore all the information it received thus far (if any) and reset to its initial state. |

### Owner ServiceInfo Module Callback
This type is a pointer to a callback function that is used to process Owner ServiceInfo messages received from the Owner Server for a specific ServiceInfo module.

It takes a pointer to the structure `fdor_t` whose internal buffer contains the entire message as received in TO2.OwnerServiceInfo (Type 69) with the current position set to the ServiceInfoVal, as well as the ServiceInfoKey (module message). The module must read the ServiceInfoVal from `fdor_t`, process it as per the ServiceInfoKey, and advance the `fdor_t` internal buffer to the next valid CBOR entry (currently being done internally in few methods). The module must treat the received `fdor_t` as a read-only structure, and must never modify it, excpet advancing to the next CBOR entry.

This callback function is invoked in the context of the executing onboarding protocol hence, although there is no fixed timeline, the module must complete execution in the shortest possible time.

_**Syntax**_

```
typedef int (*fdo_sdk_owner_service_infoCB)(
    fdo_sdk_si_type  type,
    fdor_t           *fdor,
    char             *module_message
);
```

_**Parameters**_

| | |
|-|-|
| **type** | This indicates the type of the callback as specified in the FDO Service Callback Type section. |
| **fdor** | A pointer to `fdor_t`, currently pointing to the ServiceInfoVal to be processed. |
| **module_message** | A pointer to value of ServiceInfoKey representing the module message. |

_**Return Value**_

The return value could be one of the following:

| | |
|-|-|
| **FDO_SI_CONTENT_ERROR**| Indicates that the module could not process the ServiceInfo due to invalid content of either the key or value. An error will cause the onboarding protocol to fail and be retried. |
| **FDO_SI_INTERNAL_ERROR** | Indicates that the module could not process the ServiceInfo due to an internal error. An error will cause the onboarding protocol to fail and be retried. |
| **FDO_SI_SUCCESS** | Indicates that the module was able to successfully process the ServiceInfo (or ignored it successfully). |

### ServiceInfo Module Description
This structure describes a ServiceInfo module that implements module functionality. Supported modules are known to the Application ahead of time and each module must have one ServiceInfo Module entry, which is passed to the SDK in the `fdoSdkInit()` API.

_**Syntax**_
```
typedef struct {
    bool                active;
    char                moduleName[16];
    fdo_sdk_owner_service_infoCB serviceInfoCallback;
} fdoSdkServiceInfoModule;
```
_**Members**_

| | |
|-|-|
| **active** | A bool value denoting whether the ServiceInfo module is currently active(true) or inactive(false). This value is maintained by the SDK and should not be tampered with by the module during ServiceInfo processing. |
| **moduleName** | The symbolic name of the ServiceInfo Module. This should be a NULL terminated string, no larger than 15 characters. |
| **serviceInfoCallback** | This callback function will be invoked by the SDK to pass Owner ServiceInfo to the module. This callback will be executed in the context of the onboarding protocol. See ServiceInfo Module Callback. |

### Error Handling Callback
This type is a pointer to a callback function that is used to process errors during protocol execution. The Application can use information provided by this callback to perform application-specific operations. The Application can also control the execution of the protocol state machine by return different values as specified below.

_**Syntax**_
```
typedef int (*fdoSdkErrorCB)(
    fdoSdkStatus type,
    fdoSdkError  errorCode
);
```

_**Parameters**_

| | |
|-|-|
| **type** | This value specifies the type of error that occurred. It will be one of the following values (other `fdoSdkStatus` values are not used here): <br> * `FDO_ERROR`: This indicates an unrecoverable error occurred. The SDK will continue with protocol restart for these types of errors but it is unlikely that the operation will succeed. It is advisable to abort the operation and retry later. <br> * `FDO_WARNING`: This indicates that a transient error occurred. The SDK will continue with protocol restart, which might fix the problem. It is advisable that the Application allows the restart to take place. |
| **errorCode** | This value indicates details of the error that occurred. See description in FDO SDK Error Values. |

_**Return Value**_

The return value could be one of the following constants.

***NOTE***: These values are constants that are defined in the FDO SDK header file.

| | |
|-|-|
| **FDO_SUCCESS** | Indicates that the error was handled and the SDK should continue with its recovery or restart as required. |
| **FDO_ABORT** | This causes the SDK to terminate protocol processing and return to the caller (such as, the `fdoSdkRun()` API returns). The Application can re-invoke this API later to re-initiate the FDO onboarding process. |

## SDK API Functions

The following functions are provided by the SDK and defined in the SDK API header file.

### Initialize FDO SDK

The Application must invoke this API before any other APIs since this API initializes all internal data and state of the SDK.

_**Syntax**_
```
fdoSdkStatus fdoSdkInit(
    fdoSdkErrorCB    *errorHandlingCallback,
    uint32_t          numModules,
    fdoSdkModuleInfo *moduleInformation,
);
```

_**Parameters**_

| | |
|-|-|
| **errorHandlingCallback** | This is the Application’s error handling function and will be called by the SDK when an error is encountered. This value can be `NULL` in which case, errors will not be reported to the Application, and the SDK will take the appropriate recovery and/or restart action as required. <br> ***NOTE***: Passing `NULL` might cause the SDK to remain in an infinite loop until the onboarding process completes successfully. |
| **numModules** | Number of ServiceInfo modules contained in the following `moduleInformation` list parameter. If no Application-specific modules are available, this value should be zero. |
| **moduleInformation** | Service Module Information description for each available ServiceInfo module as described in ServiceInfo Module Description. If no Application-specific modules are available, this value should be `NULL`. |

_**Return Value**_

This function returns success or an error code as defined in Generic API Return Status.

### Get Current FDO SDK Status
This function returns the current state of the FDO SDK. It may only be called after the SDK has been initialized using the `fdoSdkInit()` API.

_**Syntax**_
```
fdoSdkDeviceState fdoSdkGetStatus(void);
```

_**Return Value**_

This function returns a value of type `fdoSdkDeviceStatus` as described in FDO Device Status.

### Execute FDO SDK Onboarding Protocol
The Application invokes this API to begin the onboarding process that is, TO1. The onboarding process has completed successfully when this function returns `FDO_SUCCESS`. If this API returns an error, the Application may retry the onboarding process by calling this API again immediately or after a sleep/reset cycle as determined by the use case.
The SDK will invoke the Application error callback if an error occurs in this phase. Additionally, module-specific callbacks will be invoked when ServiceInfo is received from the Owner Server during the TO2 stage. These callbacks are invoked in the context of the callers thread and the callbacks must not call any SDK APIs since the SDK is not yet re-entrant.

_**Syntax**_
`fdoSdkStatus fdoSdkRun(void);`

_**Return Value**_

This function returns success or an error code as defined in Generic API Return Status.

### Prepare the FDO SDK for Resale
The Application invokes this API to prepare the device for resale. The SDK marks internal state to pre-TO1 and returns. After preparing the SDK for resale, a subsequent call to the `fdoSdkRun()` API will cause the SDK to perform the TO1 stage again, with new owner credentials that were updated at the end of the previous TO2 protocol stage.

_**Syntax**_
```
fdoSdkStatus fdoSdkResale(void);
```

_**Return Value**_

This function returns success or an error code as defined in Generic API Return Status

## FDO Concise Binary Object Representation (CBOR) encoder/decoder APIs

Client SDK uses APIS provided in the header file `fdoblockio.h` to handle CBOR operations (encode/decode). The API implementations wraps and internally uses the APIs provided by Intel TinyCBOR\* library. The structue for writer (encoder) and reader (decoder) are given below:

### FDO Writer (CBOR Encoder)

It is responsible for encoding the buffer inside `fdo_block_t` into CBOR-encoded stream. Internally, it uses wraps TinyCBOR's `CborEncoder` into a doubly-linked-list type structure, that is used to manage CBOR container-specific operations (CBOR map/array).

The user must perform following operations in sequence to use the FDO Writer struct `fdow_t`:

  * Allocate memory for `fdow_t` structure

  * Invoke `fdow_init()` to fill the allocated memory with 0's

  * Invoke `fdo_block_alloc()` or `fdo_block_alloc_with_size()` to allocate memory for `fdow_t.b`

  * Invoke `fdow_encoder_init()` to initialize the TinyCBOR's `CborEncoder` with the previously allocated buffer `fdow_t.b`.

_**Syntax**_
```
typedef struct _FDOW_s {
    fdo_block_t b;
    int msg_type;
    fdow_cbor_encoder_t *current;
} fdow_t;

typedef struct _FDOW_CBOR_ENCODER {
    CborEncoder cbor_encoder;
    struct _FDOW_CBOR_ENCODER *next;
    struct _FDOW_CBOR_ENCODER *previous;
} fdow_cbor_encoder_t;
```

_**Members**_

| | |
|-|-|
| **b** | The `fdo_block_t` structure containing the buffer alogwith its size. The TinyCBOR\* library uses this buffer to store the data that has been CBOR-encoded so far. |
| **msg_type** | FDO type (Type 1x/3x/6x/255) |
| **current** | A `fdow_cbor_encoder_t` structure that resembles a doubly-linked-list, containing TinyCBOR's CborEncoder. |

### FDO Reader (Decoder structure)

It is responsible for decoding the CBOR-encoded buffer inside `fdo_block_t` into binary stream. Internally, it wraps TinyCBOR's `CborValue` into a doubly-linked-list type structure, that is used to manage CBOR container-specific operations (CBOR map/array).

The user must perform following operations in sequence to use the FDO Reader struct `fdor_t`:

  * Allocate memory for `fdor_t` structure

  * Invoke `fdor_init()` to fill the allocated memory with 0's

  * Invoke `fdo_block_alloc()` or `fdo_block_alloc_with_size()` to allocate memory for `fdor_t.b`

  * Copy the CBOR-encoded stream into the previously allocated `fdor_t.b`

  * Invoke `fdor_parser_init()` to initialize the TinyCBOR's `CBORParser` with the previously allocated buffer `fdor_t.b`.

_**Syntax**_
```
typedef struct _FDOR_s {
    fdo_block_t b;
    int msg_type;
    bool have_block;
    CborParser cbor_parser;
    fdor_cbor_decoder_t *current;
} fdor_t;

typedef struct _FDOR_CBOR_DECODER {
    CborValue cbor_value;
    struct _FDOR_CBOR_DECODER *next;
    struct _FDOR_CBOR_DECODER *previous;
} fdor_cbor_decoder_t;
```

_**Members**_

| | |
|-|-|
| **b** | The `fdo_block_t` structure containing the buffer alogwith its size. The TinyCBOR* library reads the CBOR-encoded stream from this buffer and decodes them into binary stream. |
| **msg_type** | FDO type (Type 1x/3x/6x/255) |
| **have_block** | A bool value that represents whether the structure contains data to be decoded. Generally, used by protocol, before decoding is started. |
| **cbor_parser** | CborParser from the TinyCBOR* library. |
| **current** | The `fdow_cbor_decoder_t` structure that resembles a doubly-linked-list, containing TinyCBOR's CborValue. |
