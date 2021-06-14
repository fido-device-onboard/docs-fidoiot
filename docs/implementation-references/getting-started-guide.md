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

!!! NOTE
    For the instructions in this document, `<fdo-pri-src>` refers to the path of the FDO PRI folder 'pri-fidoiot'.

FDO PRI source can be built in two ways:

1. Using the maven build system to build FDO PRI source.

```
$ cd <fdo-pri-src>
$ mvn clean install
```

2.&nbsp;Using docker container to build FDO PRI source

```
$ cd <fdo-pri-src>/build
$ sudo docker-compose up --build
```

The build stage generates artifacts and stores them in `component-samples/demo` directory.

!!! NOTE
    - If working behind a proxy,ensure to set proper proxy variables.
    - [Follow the steps](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/) to setup Docker* environment.
    - [Read more](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/) about PRI source building.

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
    - Proper [keystore management](#keystore-management) to be considered before using the services in production environment.
    - To0scheduling interval property can be modified in the component-sample/demo/owner/owner.env.
      Update `owner_to0_scheduling_interval=30`
    - [Read more](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md) about starting PRI services.

## Running E2E for PRI device

1. Follow the above [instructions](#starting-fdo-service-containers) to start FDO Service Containers.


2. Start Device Initialization (DI)

```
$ cd <fdo-pri-src>/component-samples/demo/device
$ java -jar device.jar
```

Expect the following line on successful DI completion.

```
DI complete, GUID is <guid>
```

!!! NOTE
    - Additional arguments for [configuring PRI device](https://github.com/secure-device-onboard/pri-fidoiot/tree/master/component-samples/demo/device#configuring-the-device-service).
    - Configuring PRI device for [HTTPS/TLS communication](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md).
    - [Read more](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/device/README.md) about Device Intialization.


3.&nbsp;Voucher Extension for PRI device

    $ curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/0 -o voucher

    $ curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/

Wait for T00 finished for <guid> message in the Owner console.

Expect the following message on successful TO0 completion.

```
To0 Response Wait for <guid> : 3600
TO0 Client finished for GUID <guid>
```

!!! NOTE
    - [Keystore Management](#keystore-management) needs to be taken care, if PRI Rendezvous server and PRI Owner server is not running on the same machine.
    - **You can enable ServiceInfo at this stage.** [Follow the instructions](#enabling-serviceinfo-transfer) to enable ServiceInfo.

4.&nbsp;T01 and T02
   ```
   $ cd <fdo-pri-src>/component-samples/demo/device
   $ java -jar device.jar
   ```

Wait for T0 protocol completed message and Device is Onboarded Successfully.

Expect the following message on successful TO completion.

```
TO2 complete, GUID is d43c6dc6...
```

## Building Client-SDK Source

!!! NOTE
    For the instructions in this document, `<client-sdk-src>` refers to the path of the FDO Client-SDK source folder 'client-sdk-fidoiot'.

FDO Client-SDK source can be build by:

1. Follow instructions in the [documentation](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md) to install dependencies.


2. Execute build.sh script

```
$ cd <client-sdk-src>
$ ./build.sh
```

The build script generates artifacts and stores them in `./build/` directory.

## Running E2E demo for FDO Client-SDK.

1. Follow the [instructions](#starting-fdo-service-containers) to start FDO Service Containers.


2. Start Device Initialization (DI)

```
$ cd <client-sdk-src>
$ ./build/linux-client
```

!!! NOTE
    - [Read more](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md#7-running-the-application-) on Client-SDK Device Initialization.
    - [Configuring Client-SDK device](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#7-http-proxy-configuration-optional) for Proxy Network.
    - Follow instructions in the [documentation](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#3-setting-the-manufacturer-network-address), to update Manufacturer's address.


3.&nbsp;Voucher Extension for Client-SDK device

```
$ curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/abcdef -o voucher

$ curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/
```

Wait for T00 finished for <guid> message on the Owner console.

Expect the following message on successful TO0 completion.

```
To0 Response Wait for <guid> : 3600
TO0 Client finished for GUID <guid>
```

!!! NOTE
    - [Keystore Management](#keystore-management) needs to be taken care, if PRI Rendezvous server and PRI Owner server is not running on the same machine.
    - **You can enable ServiceInfo at this stage.** [Follow the instructions](#enabling-serviceinfo-transfer) to enable ServiceInfo.

4.&nbsp;T01 and T02

```
$ cd <client-sdk-src>
$ ./build/linux-client
```

Wait for T0 protocol completed message

Expect the following message on successful TO completion.

```
Device onboarded successfully.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@FIDO Device Onboard Complete@
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

## Enabling ServiceInfo transfer


1. Activating ServiceInfo Module.

        $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=active&priority=0&bytes=F5' --header 'Content-Type: application/octet-stream'

    In this example, we are activating fdo_sys module.
    http://localhost:8042 can be changed with the IP address of the owner.


2. Transferring payload or executable resource for a specific type of device.

    - Create a sample linux64.sh shell script.

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

    This script downloads the SECURITY.md file and checks the integrity of file against the pre-computed checksum value.

    -  Curl command to transfer executable resource.

            $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=filedesc&priority=1&filename=linux64.sh&device=FDO-Pri-Device' --header 'Content-Type: application/octet-stream' --data-binary '@path-to-executable/linux64.sh'

    - for Client-SDK devices, update device parameter to `Intel-FDO-Linux`. Eg:

            $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=filedesc&priority=1&filename=linux64.sh&device=Intel-FDO-Linux' --header 'Content-Type: application/octet-stream' --data-binary '@path-to-executable/linux64.sh'


3. Curl command to add exec command on the transferred script

            $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=exec&guid=<guid-of-device>&priority=2&bytes=82672F62696E2F73686A6C696E757836342E7368' --header 'Content-Type: application/octet-stream'

    !!! NOTE
        bytes parameter is the cbor equivalent of ./linux64.sh.
        You can skip step 3, if you are just transferring a payload.

## Keystore Management

### Generating self-signed keys for HTTPS/TLS communication.

1. Generate key and certificate.

        openssl req \
        -x509 \
        -newkey rsa:2048 \
        -sha256 \
        -days 3560 \
        -nodes \
        -keyout tls.key \
        -out tls.crt \
        -subj '/CN=fdo' \
        -extensions san \
        -config <( \
          echo '[req]'; \
          echo 'distinguished_name=req'; \
          echo '[san]'; \
          echo 'subjectAltName=DNS:localhost,IP:<ip-address>')


    Update `<ip-address>` with the ip address of machine running the FDO service.


2. Generate Keystore using fresh key and certificate.

    ```
    openssl pkcs12 -export -in tls.crt -inkey tls.key -out ssl.p12
    ```

    - Copy the `ssl.p12` file into `component-samples/demo/<component>/certs/` folder.
    - Update the respective `.env` file with proper keystore credentials.

3. Adding generated certificate into the truststore.

    ```
    keytool -import -alias fdo -file tls.crt -storetype PKCS12 -keystore truststore
    ```

!!!NOTE
    - [Read more](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md#generating-key-pair) about key generation.
    - You can update the key type, by modifying the `-newkey` attribute during the key generation stage.
    - You can add multiple ip addresses in the `subjectAltName` attribute.