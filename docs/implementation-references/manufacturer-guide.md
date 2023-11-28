## Introduction
FIDO Device Onboard (FDO) enhances the out-of-the-box setup and provisioning experience for connected IoT devices. The FIDO Device Onboard project provides the manufacturer component sample for manufacturers (OEMs, ODMs, or 3rd party integrators) to enable FDO on their devices. This document details how a manufacturer can use this tool to produce an FDO-enabled device. 

### Terminology
Refer to the [FIDO Device Onboard Reference page](../reference.md).

## Overview
To produce FIDO Device Onboard (FDO)-enabled devices, a manufacturer must run the FIDO device initialization (DI) protocol on the device. The DI process sets security 
credentials in secure storage in the device and creates the initial ownership voucher.

The manufacturer makes use of the provided tools for this process: a manufacturer component sample (which includes an embedded H2 database), and software that runs on the device to perform DI. The manufacturer component sample acts as a DI server for the devices. After running DI on the device, the resulting ownership voucher is stored in the H2 Database. At this point, the FDO security credentials have been stored in the device and the device is FDO ready. When the device is sold to a customer, the ownership voucher is extended and shipped along with the device. A complete integration and deployment of FDO in a manufacturing environment requires a fair amount of planning and effort. However, for development, test, and enabling purposes, a much simpler evaluation deployment can be done. 

For details on how to setup and initialize the Manufacturer Component Sample see the Readme included with this sample as part of the FDO software [Manufacturer Component Readme](https://github.com/fido-device-onboard/pri-fidoiot/tree/master/component-samples/demo/manufacturer/README.md).

### Manufacturer Component Sample
The main function of the manufacturer component sample is to serve as a DI protocol server. The manufacturer component sample runs as a web service and makes use of the FDO H2 database for configuration and storage of ownership vouchers. All configuration and data for the manufacturer is stored in this H2 database and property or environment files. 

## Evaluation Deployment
The evaluation deployment is useful for development, test, and enabling purposes. The evaluation deployment can fully initialize a device to the same extent as the production deployment but does not require any integration with business systems.
### Step 1: Prepare Hardware Infrastructure
You need to have one machine that can run the manufacturer component sample. A non-production environment has minimal requirements; the machine can run either the Linux* OS (version 20.04) or Windows\* 10 OS.
### Step 2: Component Sample Installation
Install the manufacturer component sample, see [Manufacturer Component Readme](https://github.com/fido-device-onboard/pri-fidoiot/tree/master/component-samples/demo/manufacturer/README.md) for details. 
### Step 3: Test
Set up the device to run the FDO DI protocol (refer to device specific documentation for details). Run DI on the device against the manufacturer component sample. You can use any value for the device serial no at this point. For a production scenario, see [Device Serial No](device-mfg-info.md) for details. Use the REST APIs (or a database management tool) to view and confirm the ownership voucher has been created.

## Production Deployment
A production deployment requires all steps of an evaluation deployment with the addition of integration with the manufacturing business systems. In addition, the manufacturer component sample is just a sample and will require further enchancements to make it production worthy.

### Device Serial Number
Since a device identifier (typically, a serial number) is required for the DI protocol, it is recommended that there be some programmatic mechanism to determine the serial no of a device. This value can then be passed to the FDO client software to be encorporated into the appropriate DI message. This may require modifications to your existing manufacturing workflow.

### Manage FDO public key(s) from customers, and the public key(s) storage in the H2 database. 
FDO requires that a customer create (or make use of existing) a key pair to use for extending ownership vouchers. The public key of this pair must be provided to the manufacturer to use for extending the voucher to that customer. Your customer interactions and processes must be modified as required to support this receipt of their public key and then to import this key into the manufacturer component sample. Whether this key is delivered with each order or done before separately depends on your processes and interaction with your customers.
Note: depending on the type of devices you manufacture, more than one type of key pair may be required (i.e. ECC384, RSA256, etc.).

### Ownership Vouchers Management
After a device is initialized with FDO, an initial ownership voucher is produced and stored in the database. This voucher must first be “signed” (extended) to a customer before sending to the customer. The ownership voucher management task has the following two steps: 
1.	Assign a voucher (device) to a customer (technically, a customer public key). 
2.	Perform voucher extension. The resulting extended voucher can then be sent to the customer.
The following are the implied requirements:
1.	It is assumed that you already have some process to track which device was shipped with which order. Most likely, this is done with serial numbers. With FDO, there is an additional requirement that you match voucher(s) with physical devices such that the voucher(s) delivered to the customer are those that correspond to the actual physical devices that are delivered.
2.	You need to update or modify the existing B2B channel to include an ownership voucher for each physical device delivered to the customer. Typically, these vouchers would be linked to the order in a particular way but the details will vary for each manufacturer.
3.	If a customer loses one of these vouchers and requires a replacement, then you must re-generate this voucher (requires physical access to the device) or retrieve the voucher from the stored data if it chose to store the extended voucher.

### Security Considerations
* The network used to connect devices to the manufacturer component sample must be on a closed network. Since DI is used to set security credentials into the device, it is vunerable to attacks if exposed externally. 
* The key pair used by the manufacturer to extend vouchers must be stored securely. It is recommended that the system that stores the keystore file be secured from physical access to only qualified individuals. 

### Supply Chain Requirements
FDO requires support from your supply chain partners. Specially, they will need to deploy the means to manage and extend ownership vouchers. As physical devices come into their inventory they will need to associate those devices with the respective ownership vouchers. Then as those device are resold, the vouchers will need to be extended to the next owner in the supply chain. 

To support this process, there is a reseller component sample that can perform the storage and extension of ownership vouchers. See [Component Reseller](https://github.com/fido-device-onboard/pri-fidoiot/tree/master/component-samples/demo/reseller) for details.
