This section defines the “system” module (fdo_sys) specification which provides basic onboarding services
for FDO capable devices. A module is a set of key-value pairs; they define the onboarding operations
that can be performed on a given FDO device. Module key-value pairs are exchanged between the device
and it’s owning Device Management Service. It is up to the owning management service and the
device to interpret the key-value pairs in accordance with the module specification.

## fdo_sys Module Definition
The system module defines basic onboarding operations such as downloading content and executing
commands. The following table describes key-value pairs for the system module.

| **Key Name** | **Value** | **Meaning** |
|-|-|-|
| fdo_sys:active | CBOR Bool | True to activate the fdo_sys module. |
| fdo_sys:exec | CBOR Array of String values | The command is put as array elements. The command performs a system call. |
| fdo_sys:filedesc | CBOR String | Describes a path to a file that will be used in subsequent file operations. |
| fdo_sys:write | CBOR BSTR | Appends a block of data to the current file description. |

### fdo_sys:exec Message

The fdo_sys:exec command performs a system call on the device. The value of this command is a
CBOR Array of String values. The first string in the array is the command to execute and the
remaining strings are the arguments to the command. It is expected that it would use “exec”
(system call) or similar API to execute the command on the device.

| | |
|-|-|
| JSON* (readable description) | ["/bin/sh","startup.sh"] |
| CBOR | `82` # array(2) <br> `67` # text(7) <br> `2F62696E2F7368` # "/bin/sh" <br> `6A` # text(10) <br> `737461727475702E7368` # "startup.sh"|
| C example | execvp(“/bin/sh”,(char* []) {“shartup.sh”}) |

### fdo_sys:filedesc Message

The fdo_sys:filedesc message describes the path to a file the will be used as a part of the
on-boarding process. A zero-length file is expected to exist on the local file system after this
command is received. If the described file already exists, it is truncated to zero length, otherwise
a zero-length file is created. The permissions for the created file is set to read/write
for the user account the module is running under. File permissions can subsequently be modified with
the fdo_sys:exec command if needed. If a path is not included as a part of the file name, the
file is created under current working directory of the module. All subsequent fdo_sys:write
operations will append to this file. If another fdo_sys:filedesc message is received, all subsequent
fdo_sys:write messages will start appending to the file specified by the fdo_sys:filedesc message.

### fdo_sys:write Message

The fdo_sys:write message provides an array of bytes that gets appended to the file described by
the last fdo_sys:filedesc message. If this message is sent without being preceded by
fdo_sys:filedesc, then an message `255: INVALID_MESSAGE_ERROR` will be thrown and TO2 will not be
completed. Once a fdo_sys:filedesc message has been received, many fdo_sys:write messages can
follow.

## Examples
Below are examples of the fdo_sys messages encoded as CBOR. The JSON examples are just human
readable definitions while the actual messages are always CBOR. The encoding includes the entire
TO2.OwnerServiceInfo message include the isMore and isDone flags.

Device should advertise it supports fdo_sys.

***NOTE***: This example just includes the module list key value pairs and not all the required values for the devmod.

### DeviceServiceInfo (devmod)

| | |
|-|-|
| Diagnostic Notation (not used by protocol) | [false, [[["devmod:active", "true"], ["devmod:nummodules", 1], ["devmod:modules", [1, 1, "fdo_sys"]]]]] |
| CBOR | `82` # array(2) <br> `F4` # primitive(20) - IsMoreSeviceInfo <br> `81` # array(1) - ServiceInfo <br> `83` # array(3) - ServiceInfo Key-Value Array <br> `82` # array(2) - ServiceInfo Key-Value Pair 1 <br> `6D` # text(13) <br> `6465766D6F643A616374697665` # "devmod:active" <br> `64` # text(4) <br> `74727565` # "true" <br> `82` # array(2) - ServiceInfo Key-Value Pair 2 <br> `71` # text(17) <br> `6465766D6F643A6E756D6D6F64756C6573` # "devmod:nummodules" <br> `01` # unsigned(1) <br> `82` # array(2) - ServiceInfo Key-Value Pair 3 <br> `6E` # text(14) <br> `6465766D6F643A6D6F64756C6573` # "devmod:modules" <br> `83` # array(3) <br> `01` # unsigned(1) - Module Index <br> `01` # unsigned(1) - Number of modules <br> `67` # text(7) <br> `66646F5F737973` # "fdo_sys" |

### OwnerServiceInfo

#### fdo_sys:active
| | |
|-|-|
| Diagnostic Notation (not used by protocol) | [true, false, [[["fdo_sys:active", true]]]] |
| CBOR | `83` # array(3) <br> `F5` # primitive(21) - IsMoreSeviceInfo <br> `F4` # primitive(20) - IsDone <br> `81` # array(1) - ServiceInfo <br> `81` # array(1) - ServiceInfo Key-Value Array <br> `82` # array(2) - ServiceInfo Key-Value Pair <br> `6E` # text(14) <br> `66646F5F7379733A616374697665` # "fdo_sys:active" <br> `F5` # primitive(21) |

#### fdo_sys:filedesc
| | |
|-|-|
| Diagnostic Notation (not used by protocol) | [true, false, [[["fdo_sys:filedesc", "startup.bat"]]]] |
| CBOR | `83` # array(3) <br> `F5` # primitive(21) - IsMoreSeviceInfo <br> `F4` # primitive(20) - IsDone <br> `81` # array(1) - ServiceInfo <br> `81` # array(1) - ServiceInfo Key-Value Array <br> `82` # array(2) - ServiceInfo Key-Value Pair <br> `70` # text(16) <br> `66646F5F7379733A66696C6564657363` # "fdo_sys:filedesc" <br> `6B` # text(11) <br> `737461727475702E626174` # "startup.bat" |

#### fdo_sys:write
| | |
|-|-|
| Diagnostic Notation (not used by protocol) | [true, false, [[["fdo_sys:write", h'4045…']]]] |
| CBOR | `83` # array(3) <br> `F5` # primitive(21) - IsMoreSeviceInfo <br> `F4` # primitive(20) - IsDone <br> `81` # array(1) - ServiceInfo <br> `81` # array(1) - ServiceInfo Key-Value Array <br> `82` # array(2) - ServiceInfo Key-Value Pair <br> `6D` # text(13) <br> `66646F5F7379733A7772697465` # "fdo_sys:write" <br> `59 0116` # bytes(278) <br> `4045..` # "@E" -rest of 278 byte file |

#### fdo_sys:exec
| | |
|-|-|
| Diagnostic Notation (not used by protocol) | [false, true, [[["fdo_sys:exec", ["cmd", "/c", "startup.bat"]]]] |
| CBOR | `83` # array(3) <br> `F4` # primitive(20) - IsMoreSeviceInfo <br> `F5` # primitive(21) - IsDone <br> `81` # array(1) - ServiceInfo <br> `81` # array(1) - ServiceInfo Key-Value Array <br> `82` # array(2) - ServiceInfo Key-Value Pair <br> `6C` # text(12) <br> `66646F5F7379733A65786563` # "fdo_sys:exec" <br> `83` # array(3) <br> `63` # text(3) <br> `636D64` # "cmd" <br> `62` # text(2) <br> `2F63` # "/c" <br> `6B` # text(11) <br> `737461727475702E626174` # "startup.bat" |

#### ismore flag

Modules should always set the 'ismore' flag to false to allow device modules to respond.  The only time 'ismore' should be true is if the message being sent is not complete processable message.  All fdo_sys messages are complete messages, so ismore can always be true.


