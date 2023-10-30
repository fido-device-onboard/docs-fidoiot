# FIDO Device Onboard (FDO) Client SDK Intel® CSE API Reference

This document describes the list of all the functions used by the client SDK to interact with the CSE (Converged Security Engine).

<!-- 1 -->

## cse_get_cert_chain()

Interface to get device certificate from CSE.

```c
int32_t cse_get_cert_chain(fdo_byte_array_t **cse_cert)
```

### Description

This function gets the device certificate from the CSE. It takes a pointer to a byte array as input, and returns a pointer to a byte array holding a valid device CSE certificate.

### Parameters

- `cse_cert`: Pointer to a byte array holding a valid device CSE certificate.

### Return Value

- `0`: If the device certificate is successfully retrieved from the CSE.
- `-1`: If there is a failure during the device certificate retrieval process.'

<!-- 2 -->

## cse_get_cose_sig_structure()

Interface to get COSE signature structure.

```c
int32_t cse_get_cose_sig_structure(fdo_byte_array_t **cose_sig_structure,
                   uint8_t *data, size_t data_len)
```

### Description

This function gets the COSE signature structure. It takes a pointer to a byte array, a pointer to a data buffer, and the size of the data buffer as input, and returns a pointer to a byte array holding a COSE signature structure.

### Parameters

- `cose_sig_structure`: Pointer to a byte array holding a COSE signature structure.
- `data`: Pointer to a data buffer.
- `data_len`: Size of the data buffer.

### Return Value

- `0`: If the COSE signature structure is successfully retrieved.
- `-1`: If there is a failure during the COSE signature structure retrieval process.

<!-- 3 -->

## cse_get_test_sig()

Interface to get test signature from CSE.

```c
int32_t cse_get_test_sig(fdo_byte_array_t **cse_signature,
             fdo_byte_array_t **cse_maroeprefix,
             fdo_byte_array_t *cose_sig_structure, uint8_t *data,
             size_t data_len)
```

### Description

This function gets the test signature from the CSE. It takes a pointer to a byte array, a pointer to a byte array, a pointer to a COSE signature structure, a pointer to a data buffer, and the size of the data buffer as input, and returns a pointer to a byte array holding a valid device CSE test signature.

### Parameters

- `cse_signature`: Pointer to a byte array holding a valid device CSE test signature.
- `cse_maroeprefix`: Pointer to a byte array holding a valid device CSE maroeprefix.
- `cose_sig_structure`: Pointer to a COSE signature structure.
- `data`: Pointer to a data buffer.
- `data_len`: Size of the data buffer.

### Return Value

- `0`: If the test signature is successfully retrieved.
- `-1`: If there is a failure during the test signature retrieval process.

<!-- 4 -->

## cse_load_file()

Loads the data from CSE storage.

```c
int32_t cse_load_file(uint32_t file_id, uint8_t *data_ptr,
          uint32_t *data_length, uint8_t *hmac_ptr, size_t hmac_sz)
```

### Description

This function loads the data from CSE storage. It takes a file ID, a pointer to a data buffer, a pointer to the size of the data buffer, a pointer to a HMAC buffer, and the size of the HMAC buffer as input, and returns a status for the API function.

### Parameters

- `file_id`: File ID type Device status or OVH.
- `data_ptr`: Pointer to a data buffer.
- `data_length`: Pointer to the size of the data buffer.
- `hmac_ptr`: Pointer to a HMAC buffer.
- `hmac_sz`: Size of the HMAC buffer.

### Return Value

- `0`: If the data is successfully loaded from CSE storage.
- `-1`: If there is a failure during the data loading process.

<!-- 5 -->

## crypto_hal_ecdsa_sign_cse()

Signs a message using CSE API.

```c
int32_t crypto_hal_ecdsa_sign_cse(const uint8_t *data, size_t data_len,
                  uint8_t *message_signature,
                  size_t message_sig_len, uint8_t *eat_maroe,
                  size_t *maroe_length)
```

### Description

This function signs a message using CSE API. It takes a pointer to a data buffer, the size of the data buffer, a pointer to a message signature buffer, the size of the message signature buffer, a pointer to a maroeprefix buffer, and the size of the maroeprefix buffer as input, and returns a status for the API function.

### Parameters

- `data`: Pointer to a data buffer.
- `data_len`: Size of the data buffer.
- `message_signature`: Pointer to a message signature buffer.
- `message_sig_len`: Size of the message signature buffer.
- `eat_maroe`: Pointer to a maroeprefix buffer.
- `maroe_length`: Size of the maroeprefix buffer.

### Return Value

- `0`: If the message is successfully signed using CSE API.
- `-1`: If there is a failure during the message signing process.

<!-- 6 -->

## crypto_hal_hmac_cse()

Calculates HMAC on input data.

```c
int32_t crypto_hal_hmac_cse(uint8_t *buffer, size_t buffer_length,
                uint8_t *output, size_t output_length)
```

### Description

This function calculates HMAC on input data. It takes a pointer to an input data buffer, the size of the input data buffer, a pointer to an output data buffer, and the size of the output data buffer as input, and returns a status for the API function.

### Parameters

- `buffer`: Pointer to an input data buffer.
- `buffer_length`: Size of the input data buffer.
- `output`: Pointer to an output data buffer.
- `output_length`: Size of the output data buffer.

### Return Value

- `0`: If the HMAC is successfully calculated on input data.
- `-1`: If there is a failure during the HMAC calculation process.

<!-- 7 -->

## read_cse_device_credentials()

Populates the dev_cred structure by loading the OVH and DS file data from CSE flash.

```c
bool read_cse_device_credentials(fdo_dev_cred_t *our_dev_cred)
```

### Description

This function populates the dev_cred structure by loading the OVH and DS file data from CSE flash. It takes a pointer to the device credentials block as input, and returns a status for the API function.

### Parameters

- `our_dev_cred`: Pointer to the device credentials block.

### Return Value

- `true`: If the dev_cred structure is successfully populated.
- `false`: If there is a failure during the dev_cred structure population process.

<!-- 8 -->

## fdo_ov_hdr_cse_load_hmac()

Given an OwnershipVoucher header (OVHeader), proceeds to load and compares with stored OVHeader from CSE. If verification succeeds, it returns the stored HMAC.

```c
bool fdo_ov_hdr_cse_load_hmac(fdo_byte_array_t *ovheader, fdo_hash_t **hmac)
```

### Description

This function given an OwnershipVoucher header (OVHeader), proceeds to load and compares with stored OVHeader from CSE. If verification succeeds, it returns the stored HMAC. It takes a pointer to the received CBOR-encoded OVHeader, and a pointer to the resulting HMAC as input, and returns a status for the API function.

### Parameters

- `ovheader`: Pointer to the received CBOR-encoded OVHeader.
- `hmac`: Pointer to the resulting HMAC.

### Return Value

- `true`: If the HMAC was successfully loaded and verified.
- `false`: If there is a failure during the HMAC loading and verification process.
