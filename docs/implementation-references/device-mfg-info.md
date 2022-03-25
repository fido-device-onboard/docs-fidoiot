###	Device-mfg-info  

The device-mfg-info (DeviceMfgInfo) is used to convey device specific information to the manufacturer tool via message 10 (DIAppStart) of the DI protocol from the device. 
An FDO application that runs on the device generates the DeviceMfgInfo value. Typically, the device serial number and device info are provided as input to this application. 
The DeviceMfgInfo must be present and cannot be empty (otherwise an error will result during the DI protocol). 

 
The DeviceMfgInfo format is a CBOR encoded array consisting of the following entries:

* First entry: (integer) `Key type identifier`
* Second entry: (string) `Serial number`
* Third entry: (string) `Device Info`
* Fourth entry: (bstr) `CSR` (ECC based device) or `device cert chain` (OnDie ECDSA based device)
    - if the device is using an ECC keypair, a CSR.
    - if the device is OnDie ECDSA then the device cert chain
* Fifth entry: (bstr) `test signature` (present only for OnDie ECDSA devices)
	
where:  
 
*  `Key type identifier` = 1 | 13 | 14  

    1 = RSA256, 13 = ECC256, 14 = ECC384  

    Identifies the type of owner key the device is prepared to parse, not the type of the device's key.

*  `Serial number` = device serial number  

	The serial number should uniquely identify the device and ideally, should be present on the device itself (such as, the label). It is used to correlate the device with its associated ownership voucher by manufacturers and resellers when shipping devices.  

*  `Device Info` = device info  

	Typically used to provided the device's model number. This data is not strictly required and can be empty if desired.  

*  `CSR` = certificate signing request.   

    The CSR is optional and is only required for ECC devices. The format of the CSR should be base 64-encoded public key cryptography standards (PKCS#10) in privacy-enhanced mail (PEM) format.
	
*  `device cert chain` = device cert chain   

    The device cert chain for OnDie ECDSA based devices.

*  `test signature` = test signature

    The signature resulting from signing of the 'Serial number' entry. This is used to validate the signing and verification mechanism during manufacturing.


