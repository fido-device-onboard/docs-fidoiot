## Introduction
FIDO Device Onboard (FDO) enhances the out-of-the-box setup and provisioning experience for connected IoT devices. The FIDO Device Onboard project provides the manufacturer component sample for manufacturers (OEMs, ODMs, or 3rd party integrators) to enable FDO on their devices. This document details how a manufacturer can use this tool to produce an FDO-enabled device.

### Terminology
Refer to the FIDO Device Onboard [Reference page](../reference.md).

## Overview
To Integrate FIDO Device Onboard (FDO)-services into a Device Management Service (DMS), a DMS must host an Owner service, manage owner cryptographic keys, ingest ownership vouchers from a supply chain, and provide onboarding and onboarding solution in the form of service info instructions. 

For details on how to setup and initialize the Owner Service Component Sample see the Readme included with this sample as part of the FDO software [Owner Component Readme](https://github.com/fido-device-onboard/pri-fidoiot/tree/master/component-samples/demo/owner/README.md).

### Owner Component Sample
The main function of the Owner component sample is to serve as a TO2 protocol server. The owner component sample runs as a web service and makes use of the FDO H2 database for configuration and storage of ownership vouchers. All configuration and data for the owner is stored in this H2 database and property or environment files. 

### All-in-One (AIO) Component Sample
In addition to the owner component, the All-in-one component can be used to host Owner, Rendezvous, and Manufacturing services all in one service.  This makes deploying and testing of FDO easier.

## Evaluation Deployment
The evaluation deployment is useful for development, test, and enabling purposes. The evaluation deployment can fully initialize a device to the same extent as the production deployment but does not require any integration with business systems.

# DMS Integration using Service Info
Owner component service info can integrate with a DMS by providing the following:

-A script to download the agent used to connect to the DMS

-The Agent Credentials to download (for example, certificates or one time tokens)

-Any connection information such as scope ID, URL, or tenant information.

***NOTE***: Service info is always encrypted even if http protocol is being used for To2 Protocol.

# DMS Credential Creation

DMS credentials can be created at the time of ownership voucher injection or when the device is running the To2 protocol.


### To Do:
Setup local Azure CLI
### https://docs.microsoft.com/en-us/cli/azure/run-azure-cli-docker
### https://docs.microsoft.com/en-us/rest/api/iothub/device/createfileuploadsasuri

Create a bash to create a device with on Azure using the bash script
Use the device uuid as the name


Steps:
1.	Run CLI docker
      a.	https://docs.microsoft.com/en-us/cli/azure/run-azure-cli-docker
      b.	“docker run -it -v ${HOME}/.ssh:/root/.ssh -v ~/Desktop/dev/Azure:/home mcr.microsoft.com/azure-cli”
2.	Log in to Azure account
      a.	“az login”
      b.	https://microsoft.com/devicelogin
3.	Create a device place holder
      a.	https://docs.microsoft.com/en-us/azure/iot-edge/how-to-manual-provision-symmetric-key?view=iotedge-2018-06&tabs=azure-cli%2Clinux
      b.	“az iot hub device-identity create --device-id [Device-name] --hub-name [hub-name] --edge-enabled”
      i.	Device-Name: UUID
      ii.	Hub-name: FDO-Edge
      c.	List of all devices registered
      i.	“az iot hub device-identity list --hub-name [hub name]”
      d.	Save the connection string
      i.	az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
      
### DPS:
#### Device-Name: DEMO-DPS
#### primary:  SHA1 Fingerprint=8FE4F67B59533FEC6C3B6A2FD2BA658863F45BD5
#### secondary: SHA1 Fingerprint=68FFFFB9703ECBA49B273A417E4166253F3C7EE2
#### Azure/certs.sh

### Device:
#### UsingFDO: Install Iot-edge containers on the target device
#### UsingFDO: Inject the Connection String into config file
#### UsingFDO: Restart IOTG containers
