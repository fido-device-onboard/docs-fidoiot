***NOTE***: This is a preliminary implementation of the [FIDO Device Onboard Spec](https://fidoalliance.org/specs/FDO/fido-device-onboard-v1.0-ps-20210323/) published by the FIDO Alliance.
The implementation is experimental and incomplete, and is not ready for use in any production capacity. Some cryptographic algorithms and encoding formats have
not been implemented, and any aspect of this implementation is subject to change.

# Getting Started Guide

<figure>
  <img src="../../images/securedeviceonboard-icon-color.png" width="250" />
</figure>

***FDO (FIDO device onboard) provides a fast and more secure way to onboard a device to any device management system. A unique feature of FDO is the ability for the device owner to select the IoT platform at a late stage in the device life cycle. The secrets or configuration data may also be created or chosen at this late stage.***

This document provides a quick walk through the E2E flow. Included in this guide:

- [Building FDO PRI Source](#building-fdo-pri-source)
- [Starting FDO Service Containers](#starting-fdo-server-side-containers)
- [Running E2E for PRI device](#running-e2e-for-pri-device)
- [Building Client-SDK Source](#building-client-sdk-source)
- [Running E2E for Client-SDK device](#running-e2e-demo-for-fdo-client-sdk)
- [Enabling ServiceInfo](#enabling-serviceinfo-transfer)
- [Keystore Management](#keystore-management)

## Building FDO PRI Source

!!! NOTES
      - Check the [System Requirements](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/#system-requirements)
      - If working behind a proxy, ensure to [set proper proxy](https://github.com/secure-device-onboard/pri-fidoiot/tree/master/component-samples/demo#configuring-proxies) variables.
      - [Follow the steps](https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/) to setup Docker* environment.
      - [Read more](https://github.com/secure-device-onboard/pri-fidoiot#building-fdo-pri-source) about PRI source building.
      - [Follow the steps](../../implementation-references/proxy-settings/) to set the right proxy settings. (Includes documentation for system wide proxy configuration)

1.&nbsp; Clone the PRI-fidoiot repository
```
git clone https://github.com/secure-device-onboard/pri-fidoiot.git
```

2.&nbsp; Build PRI-fidoiot:

FDO PRI source can be built in two ways:


!!! NOTE
      For the instructions in this document, `<fdo-pri-src>` refers to the path of the FDO PRI folder 'pri-fidoiot'.

1. Using the Maven build system to build FDO PRI source.

```
cd <fdo-pri-src>
mvn clean install
```

2.&nbsp;Using Docker container to build FDO PRI source

```
cd <fdo-pri-src>/build
sudo docker-compose up --build
```

The build stage generates artifacts and stores them in `component-samples/demo` directory.

**NOTE:** During the build stage, the following error messages may be displayed on the console. These error messages
are a result of the discrepancy of logging levels during build stage and can be ignored.
```
[ERROR] Picked up _JAVA_OPTIONS: -Dhttp.proxyHost= -Dhttp.proxyPort= -Dhttps.proxyHost= -Dhttps.proxyPort=
[ERROR] WARNING: An illegal reflective access operation has occurred
[ERROR] WARNING: Illegal reflective access by org.apache.catalina.loader.WebappClassLoaderBase to field java.io.ObjectStreamClass$Caches.localDescs
[ERROR] WARNING: Please consider reporting this to the maintainers of org.apache.catalina.loader.WebappClassLoaderBase
[ERROR] WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
[ERROR] WARNING: All illegal access operations will be denied in a future release
```

## Starting FDO Server-side Containers

<figure>
  <img src="../../images/entities.png" align="left"/>
  <figcaption> FIDO Device Onboard Entities and Entity Interconnection </figcaption>
</figure>

### Key Generation for FDO Server-side Containers

Run the below commands to generate all the required keys for the Server-side containers.

```
cd <fdo-pri-src>/component-samples/demo/scripts/
bash keys_gen.sh .
cp -r creds/* ../
```

All the generated keys are now copied to the respective components.

### Starting the FDO PRI Manufacturer Server

***FDO Manufacturer is an application that runs in the factory, which implements the initial communications with the Device, as part of the Device Initialize Protocol (DI). The manufacturer creates an Ownership Voucher based on the credentials received during DI and extends the voucher to respective owner.***    

Run the below commands, in a separate console, to start the Manufacturer.

```
cd <fdo-pri-src>/component-samples/demo/manufacturer/
sudo docker-compose up --build
```   
Once the Manufacturer has successfully started, the following output is displayed
<figure>
  <img src="../../images/manufacturer.png" align="left"/>
  <figcaption> Manufacturer getting started </figcaption>
</figure>

### Starting the FDO PRI Rendezvous (RV) Server

***RV Server is a network server or service (For example, on the Internet) that acts as a rendezvous point between a newly powered on Device and the Owner Onboarding Service.***   

Run the below commands, on a seperate console, to start the RV server.

```
cd <fdo-pri-src>/component-samples/demo/rv/
sudo docker-compose up --build
```
Once the RV instance has successfully started, the following output is displayed
<figure>
  <img src="../../images/rv.png" align="left"/>
  <figcaption> RV getting started </figcaption>
</figure>

###Starting the FDO PRI Owner Server

***Owner is an entity that is able to prove ownership to the Device using an Ownership Voucher and a private key for the last entry of the Ownership Voucher. Owner supports the transfer of Serviceinfo to the Device.***   

Run the below commands, on a separate console, to start the Owner Server.

```
cd <fdo-pri-src>/component-samples/demo/owner/
sudo docker-compose up --build
```
Once the Owner instance has successfully started, the following output is displayed
<figure>
  <img src="../../images/owner.png" align="left"/>
  <figcaption> Owner getting started </figcaption>
</figure>

!!!NOTE
      - Proper [keystore management](#keystore-management) to be considered before using the services in production environment.
      - To0scheduling interval property can be modified in the component-sample/demo/owner/owner.env.
      Update `owner_to0_scheduling_interval=30`
      - [Read more](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/README.md) about starting PRI services.

## Running E2E for PRI Device

1. [Start FDO Service Containers](#starting-fdo-server-side-containers).


2. Start Device Initialization (DI)

   ***DI includes the insertion of FDO credentials into device during the manufacturing process and creation of ownership voucher.***   
   On a new console, key in the following commands

```
cd <fdo-pri-src>/component-samples/demo/device
java -jar device.jar
```

Expect the following line on successful DI completion.

```
DI complete, GUID is <guid>
```

!!! NOTE
        - Additional arguments for [configuring PRI device](https://github.com/secure-device-onboard/pri-fidoiot/tree/master/component-samples/demo/device#configuring-the-device-service).
        - Configuring PRI device for [HTTPS/TLS communication](https://github.com/secure-device-onboard/pri-fidoiot/tree/master/component-samples/demo/device#configuring-device-for-httpstls-communication).
        - [Read more](https://github.com/secure-device-onboard/pri-fidoiot/blob/master/component-samples/demo/device/README.md) about Device Intialization.


3.&nbsp;Voucher Extension & TO0 for PRI Device

<figure>
  <img src="../../images/slide3.png"/>
  <figcaption>Voucher extension</figcaption>
</figure>

***During TO0, the FDO Owner identifies itself to Rendezvous Server, establishes the mapping of GUID to the Owner IP address. TO0 ends with RV Server having an entry in a table that associates the Device GUID with the Owner Service’s rendezvous 'blob.'***


    curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/0 -o voucher

    curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/

**Make sure you are getting status `200 OK` for the curl calls. If you are facing issue with `localhost` curl calls, try with IP address instead of localhost.**

Wait for TO0 finished for <guid> message in the Owner console. This generally takes a few minutes to complete.

Expect the following message on successful TO0 completion.

```
TO0 Response Wait for <guid> : 3600
TO0 Client finished for GUID <guid>
```

!!! NOTE
        - [Keystore Management](#keystore-management) needs to be taken care, if PRI Rendezvous server and PRI Owner server is not running on the same machine.
        - **You can enable ServiceInfo at this stage.** [Follow the instructions](#enabling-serviceinfo-transfer) to enable ServiceInfo.
        - In the above commands, if the return value is **500**, replace localhost with the IP address of the machine.

4.&nbsp;TO1 and TO2

***During T01, Device identifies itself to the Rendezvous Server. Obtains mapping to connect to the Owner’s IP address. During T02, the Device contacts Owner and establishes trust and then performs Ownership Transfer.***


```
cd <fdo-pri-src>/component-samples/demo/device
java -jar device.jar
```

Wait for TO2 protocol completed message and Device is onboarded Successfully.

Expect the following message on successful TO2 completion.

```
TO2 complete, GUID is d43c6dc6...
```

## Building Client-SDK Source

FDO Client-SDK source can be build by:

1. Follow instructions in the [documentation](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md) to install dependencies.

2. Clone the repository

    ```
    git clone https://github.com/secure-device-onboard/client-sdk-fidoiot.git
    ```

!!! NOTE
For the instructions in this document, `<client-sdk-src>` refers to the path of the FDO Client-SDK source folder 'client-sdk-fidoiot'.

3.&nbsp; Execute build.sh script

```
cd <client-sdk-src>
./build.sh
```

The build script generates artifacts and stores them in `./build/` directory.

## Running E2E Demo for FDO Client-SDK.

1. [Start FDO Service Containers](#starting-fdo-server-side-containers).


2. Start Device Initialization (DI)

```
cd <client-sdk-src>
./build/linux-client
```

!!! NOTE
- [Read more](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/linux.md#7-running-the-application-) on Client-SDK Device Initialization.
- [Configuring Client-SDK device](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#7-http-proxy-configuration-optional) for Proxy Network.
- Follow instructions in the [documentation](https://github.com/secure-device-onboard/client-sdk-fidoiot/blob/master/docs/setup.md#3-setting-the-manufacturer-network-address), to update Manufacturer's address.


3.&nbsp;Voucher Extension for Client-SDK Device

```
curl -D - --digest -u apiUser:MfgApiPass123 -XGET http://localhost:8039/api/v1/vouchers/abcdef -o voucher

curl -D - --digest -u apiUser:OwnerApiPass123 --header "Content-Type: application/cbor" --data-binary @voucher http://localhost:8042/api/v1/owner/vouchers/
```

**Make sure you are getting status `200 OK` for the curl calls. If you are facing issue with `localhost` curl calls, try with IP address instead of localhost.**

Wait for TO0 to finish for <guid> message on the Owner console.

Expect the following message on successful TO0 completion.

```
TO0 Response Wait for <guid> : 3600
TO0 Client finished for GUID <guid>
```

!!! NOTE
- [Keystore Management](#keystore-management) needs to be taken care, if PRI Rendezvous server and PRI Owner server is not running on the same machine.
- **You can enable ServiceInfo at this stage.** [Follow the instructions](#enabling-serviceinfo-transfer) to enable ServiceInfo.

4.&nbsp;TO1 and TO2

```
cd <client-sdk-src>
./build/linux-client
```

Wait for TO2 protocol completed message

Expect the following message on successful TO completion.

```
Device onboarded successfully.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@FIDO Device Onboard Complete@
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

## Enabling ServiceInfo Transfer

1. Activating ServiceInfo Module.

        $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=active&priority=0&bytes=F5' --header 'Content-Type: application/octet-stream'

   In this example, we are activating fdo_sys module.
   http://localhost:8042 can be changed with the IP address of the owner.


&nbsp;2. Transferring payload or executable resource for a specific type of device.

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


&nbsp;3. Curl command to add exec command on the transferred script

            $ curl --location --digest -u apiUser:OwnerApiPass123 --request PUT 'http://localhost:8042/api/v1/device/svi?module=fdo_sys&var=exec&guid=<guid-of-device>&priority=2&bytes=82672F62696E2F73686A6C696E757836342E7368' --header 'Content-Type: application/octet-stream'

!!! NOTE
        bytes parameter is the cbor equivalent of ./linux64.sh.
        You can skip step 3, if you are just transferring a payload.

## Keystore Management

### Generating Self-signed keys for HTTPS/TLS Communication.

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


    Update `<ip-address>` with the IP address of machine running the FDO service.


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
- You can add multiple IP addresses in the `subjectAltName` attribute.
