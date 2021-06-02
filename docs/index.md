The FDO project is developing an implementation of the [FIDO Device Onboard Specification](https://fidoalliance.org/specs/FDO/fido-device-onboard-v1.0-ps-20210323/) by the FIDO Alliance.

The [FDO PRI](https://github.com/secure-device-onboard/pri-fidoiot) is a reference implementation of the FDO Specification, and also implements the service modules required to exercise the complete system: Device, Manufacturer, Rendezvous Server, and Owner.

The [FDO Client SDK](https://github.com/secure-device-onboard/client-sdk-fidoiot) is a C-based implementation of the Device component defined in FIDO Device Onboard (FDO) Specification. 

The [Implementation Reference](implementation-reference) includes different guides for integrating
FDO components for various use-cases.

See the [release notes](https://github.com/secure-device-onboard/release-fidoiot/releases) for a summary of features and capabilities implemented (or not) in different releases.

## Project Repositories

Component | Source Repository
------------------------------------|----------------------------------------------------------
Client SDK | <https://github.com/secure-device-onboard/client-sdk-fidoiot>
Protocol Reference Implementation | <https://github.com/secure-device-onboard/pri-fidoiot>
EPID Verification Service | <https://github.com/secure-device-onboard/epid-verification-service>
CI Test | <https://github.com/secure-device-onboard/test-fidoiot>
