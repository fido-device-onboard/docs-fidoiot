***NOTE***: This is a preliminary implementation of the [FIDO Device Onboard Spec](https://fidoalliance.org/specs/FDO/fido-device-onboard-v1.0-ps-20210323/) published by the FIDO Alliance.
The implementation is experimental and incomplete, and is not ready for use in any production capacity. Some cryptographic algorithms and encoding formats have
not been implemented, and any aspect of this implementation is subject to change.

# Getting Started Guide

Quick walk through the E2E flow, so you can get started. Included in this guide:
- [Building FDO PRI Source](#building-fdo-pri-source)
- [Starting FDO Service Containers](#starting-fdo-service-containers)
- [Running E2E for PRI device](#running-e2e-for-pri-device)
- [Building Client-SDK Source](#building-client-sdk-source)
- [Running E2E for Client-SDK device](#running-e2e-for-client-sdk-device)
- [Enabling ServiceInfo](#enabling-serviceinfo-transfer)
- [Keystore Management](#keystore-management)

## Building FDO PRI Source

!!! NOTE For the instructions in this document, `<fdo-pri-src>` refers to the path of the FDO PRI folder 'pri-fidoiot'.

FDO PRI source can be build in two ways:

1. Using the maven build system to build FDO PRI source.
   ```
   $ cd <fdo-pri-src>
   $ mvn clean install
   ```

2. Using docker containers to build FDO PRI source

   ```
   $ cd <fdo-pri-src>/build
   $ sudo docker-compose up --build
   ```

The build stage generates artifacts and stores it in `component-samples/demo` directory.

!!! NOTE
- If working behind a proxy,ensure to set proper proxy variables.
- Steps to run and build docker-compose up [REFER](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/)
- Read more about PRI source building. [REFER](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/)

## Starting FDO Service Containers

### Starting the FDO PRI Manufacturer Server

```
$ cd <fdo-pri-src>/component-samples/demo/manufacturer/
$ sudo docker-compose up --build
```

### Starting the FDO PRI Rendezvous (RV) Server

```
$ cd <fdo-pri-src>/component-samples/demo/rv/
$ sudo docker-compose up --build
```

###Starting the FDO PRI Owner Server

```
$ cd <fdo-pri-src>/component-samples/demo/owner/
$ sudo docker-compose up --build
```

!!!NOTE
- Proper keystore management to be considered before using the services in production environment.
- To0scheduling interval can be adjusted in the component-sample/demo/owner/owner.env
- Read more [REFER](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md)

## Running E2E for PRI device

1. Start FDO Service Containers. [REFER](#starting-fdo-service-containers)


2. Start Device Initialization (DI)
    ```
    $ cd <fdo-pri-src>/component-samples/demo/device
    $ java -jar device.jar
    ```

!!! NOTE
- Additional arguments for configuring PRI device. [REFER](https://github.com/secure-device-onboard/pri-fidoiot/tree/master/component-samples/demo/device#configuring-the-device-service)
- Configuring for HTTPS/TLS communication. [REFER](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md)
- READ MORE.[REFER](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/device/README.md)

3. Voucher Extension for PRI device
    ```
    $ curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/0 -o voucher

    $ curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/
    ```

Wait for T00 finished for <guid> message in the Owner console.

!!! NOTE
- Keystore Management needs to be taken care, if rv and owner is not running on the same machine [REFER](#keystore-management).
- **You can enable ServiceInfo at this stage.** [REFER](#enabling-serviceinfo-transfer)

4. T01 and T02
   ```
   $ cd <fdo-pri-src>/component-samples/demo/device
   $ java -jar device.jar
   ```

Wait for T0 protocol completed message and Device is Onboarded Successfully.

# Running E2E for Client-SDK device

## Building Client-SDK Source

!!! NOTE For the instructions in this document, `<client-sdk-src>` refers to the path of the FDO client-sdk folder 'client-sdk-fidoiot'.

FDO Client-SDK source can be build by:

1. Install Dependencies. [REFER](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md)


2. Execute build.sh script

   ```
   $ cd <client-sdk-src>
   $ ./build.sh
   ```

The build script generates artifacts and stores it in `./build/` directory.

## Running E2E demo for FDO Client-SDK.

1. Start FDO Service Containers. [REFER](#starting-fdo-service-containers)


2. Start Device Initialization (DI)
   ```
   $ cd <client-sdk-src>
   $ ./build/linux-client
   ```

!!! NOTE
- Read more on Client-SDK DI. [REFER](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md#7-running-the-application-)
- Configuring device for Proxy Network. [REFER](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#7-http-proxy-configuration-optional)
- To update Manufacturer's address. [REFER](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#3-setting-the-manufacturer-network-address)


3. Voucher Extension for Client-SDK device
   ```
   $ curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/abcdef -o voucher

   $ curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/
   ```

Wait for T00 finished for <guid> message on the Owner console.


4. T01 and T02
   ```
   $ cd <client-sdk-src>
   $ ./build/linux-client
   ```

Wait for T0 protocol completed message

## Enabling ServiceInfo transfer
1. Activating ServiceInfo Module.
```
$ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=active&priority=0&bytes=F5' --header 'Content-Type: application/octet-stream'
```
In this example, we are activating fdo_sys module.
http://localhost:8042 can be changed with the IP address of the owner.

2. Transferring payload or executable resource for a specific type of device.

- Create a sample linux64.sh shell script.
    ```
    #!/bin/bash
    wget https://raw.githubusercontent.com/secure-device-onboard/pri-fidoiot/master/SECURITY.md
    filename=SECURITY.md
    cksum_tx=2749598590
    cksum_rx=$(cksum $filename | cut -d ' ' -f 1)
    if [ $cksum_tx -eq $cksum_rx  ]; then
      echo "Device onboarded successfully."
      echo "Device onboarded successfully." > result.txt
    else
      echo "ServiceInfo file transmission failed."
      echo "ServiceInfo file transmission failed." > result.txt
    fi
    ```
This script downloads the SECURITY.MD file and checks the integrity of file against the pre-computed checksum value.

-  Curl command to transfer executable resource.
    ```
    $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=filedesc&priority=1&filename=linux64.sh&device=FDO-Pri-Device' --header 'Content-Type: application/octet-stream' --data-binary '@path-to-executable/linux64.sh'
    ```

- for Client-SDK devices, update device parameter to `Intel-FDO-Linux`. Eg:
  ```
  $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=filedesc&priority=1&filename=linux64.sh&device=Intel-FDO-Linux' --header 'Content-Type: application/octet-stream' --data-binary '@path-to-executable/linux64.sh'
  ```

3. Curl command to add exec command on the transferred script
   ```
    $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=exec&guid=<guid-of-device>&priority=2&bytes=82672F62696E2F73686A6C696E757836342E7368' --header 'Content-Type: application/octet-stream'
    ```

**NOTE:**
bytes parameter is the cbor equivalent of ./linux64.sh
You can skip step 3, if you are just transferring a payload.

## Keystore Management
