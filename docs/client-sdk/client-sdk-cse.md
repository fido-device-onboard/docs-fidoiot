# FIDO Device Onboard (FDO) Client SDK CSE API Reference

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
