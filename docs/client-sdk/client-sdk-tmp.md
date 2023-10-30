# FIDO Device Onboard (FDO) Client SDK TPM API Reference

This section describes list of all the functions used by the client SDK to interact with the TPM.

<!-- 1 -->
## fdo_tpm_get_hmac()

Generates HMAC using TPM.

```c
int32_t fdo_tpm_get_hmac(const uint8_t *data, size_t data_length, uint8_t *hmac,
                         size_t hmac_length, char *tpmHMACPub_key,
                         char *tpmHMACPriv_key);
```

### Description

This function generates HMAC (Hash-based Message Authentication Code) using the Trusted Platform Module (TPM). It takes input data, calculates its HMAC using TPM, and stores the result in the output buffer provided.

### Parameters

- `data`: Pointer to the input data for which HMAC needs to be generated.
- `data_length`: Length of the input data in bytes.
- `hmac`: Pointer to the output buffer where the generated HMAC will be saved.
- `hmac_length`: Length of the output HMAC buffer in bytes.
- `tpmHMACPub_key`: File name of the TPM HMAC public key. (It's likely used to access the TPM key for HMAC generation.)
- `tpmHMACPriv_key`: File name of the TPM HMAC private key. (This key is used along with the public key for HMAC generation.)

### Return Value

- `0`: If the HMAC generation is successful.
- `-1`: If there is a failure during the HMAC generation process.


<!-- 2 -->

## fdo_tpm_generate_hmac_key()

Generates HMAC key inside TPM.

```c
int32_t fdo_tpm_generate_hmac_key(char *tpmHMACPub_key, char *tpmHMACPriv_key);
```

### Description

This function generates HMAC key inside the TPM. It takes the file names of the TPM HMAC public and private keys as input.

### Parameters

- `tpmHMACPub_key`: File name of the TPM HMAC public key.
- `tpmHMACPriv_key`: File name of the TPM HMAC private key.

### Return Value

- `0`: If the HMAC key generation is successful.
- `-1`: If there is a failure during the HMAC key generation process.

<!-- 3 -->

## fdoTPMGenerate_primary_key_context()

Generates TPM primary key context from the endorsement hierarchy.

```c
int32_t fdoTPMGenerate_primary_key_context(ESYS_CONTEXT **esys_context,
                                             ESYS_TR *primary_key_handle,
                                             ESYS_TR *auth_session_handle);
```

### Description

This function generates the TPM primary key context from the endorsement hierarchy. It takes the Esys context, primary key handle, and authentication session handle as input.

### Parameters

- `esys_context`: Output Esys context.
- `primary_key_handle`: Output primary key handle.
- `auth_session_handle`: Output authentication session handle for Esys API.

### Return Value

- `0`: If the TPM primary key context generation is successful.
- `-1`: If there is a failure during the TPM primary key context generation process.

<!-- 4 -->

## fdoTPMEsys_context_init()

Initializes the Esys context.

```c
int32_t fdoTPMEsys_context_init(ESYS_CONTEXT **esys_context);
```

### Description

This function initializes the Esys context. It takes the Esys context as input.

### Parameters

- `esys_context`: Output Esys context.

### Return Value

- `0`: If the Esys context initialization is successful.
- `-1`: If there is a failure during the Esys context initialization process.

<!-- 5 -->

## fdoTPMEsys_auth_session_init()

Creates HMAC-based authentication session for Esys context.

```c
int32_t fdoTPMEsys_auth_session_init(ESYS_CONTEXT *esys_context,
                                       ESYS_TR *session_handle);
```

### Description

This function creates HMAC-based authentication session for Esys context. It takes the Esys context and authentication session handle as input.

### Parameters

- `esys_context`: Input Esys context.
- `session_handle`: Output authentication session handle.

### Return Value

- `0`: If the HMAC-based authentication session creation is successful.
- `-1`: If there is a failure during the HMAC-based authentication session creation process.

<!-- 6 -->

## fdoTPMTSSContext_clean_up()

Clears the Esys, TCTI, contexts, and authentication session, primary key handles.

```c
int32_t fdoTPMTSSContext_clean_up(ESYS_CONTEXT **esys_context,
                                    ESYS_TR *auth_session_handle,
                                    ESYS_TR *primary_handle);
```

### Description

This function clears the Esys, TCTI, contexts, and authentication session, primary key handles. It takes the Esys context, authentication session handle, and primary key handle as input.

### Parameters

- `esys_context`: Input Esys context.
- `auth_session_handle`: Input authentication session handle.
- `primary_handle`: Input primary key handle.

### Return Value

- `0`: If the Esys, TCTI, contexts, and authentication session, primary key handles are cleared successfully.
- `-1`: If there is a failure during the Esys, TCTI, contexts, and authentication session, primary key handles clearing process.

<!-- 7 -->

## fdo_tpm_commit_replacement_hmac_key()

Replaces the TPM HMAC private and public keys with the replacement keys.

```c
int32_t fdo_tpm_commit_replacement_hmac_key(void);
```

### Description

This function replaces the TPM HMAC private and public keys with the replacement keys.

### Parameters

None.

### Return Value

- `0`: If the replacement of the TPM HMAC private and public keys is successful.
- `-1`: If there is a failure during the replacement of the TPM HMAC private and public keys process.

<!-- 8 -->

## fdo_tpm_clear_replacement_hmac_key()

Clears the replacement TPM HMAC key objects, if they exist.

```c
void fdo_tpm_clear_replacement_hmac_key(void);
```

### Description

This function clears the replacement TPM HMAC key objects, if they exist.

### Parameters

None.

### Return Value

None.

<!-- 9 -->

## is_valid_tpm_data_protection_key_present()

Checks whether a valid data integrity protection HMAC key is present or not.

```c
int32_t is_valid_tpm_data_protection_key_present(void);
```

### Description

This function checks whether a valid data integrity protection HMAC key is present or not.

### Parameters

None.

### Return Value

- `1`: If a valid data integrity protection HMAC key is present.
- `0`: If a valid data integrity protection HMAC key is not present.

<!-- 10 -->

## crypto_hal_ecdsa_sign()

Signs a message using provided ECDSA private keys.

```c
int32_t crypto_hal_ecdsa_sign(const uint8_t *data, size_t data_len,
                              unsigned char *message_signature,
                              size_t *signature_length);
```

### Description

This function signs a message using provided ECDSA private keys. It takes the message, message length, message signature, and signature length as input.

### Parameters

- `data`: Input message.
- `data_len`: Input message length.
- `message_signature`: Output message signature.
- `signature_length`: Output signature length.

### Return Value

- `0`: If the message signing is successful.
- `-1`: If there is a failure during the message signing process.

<!-- 11 -->

## Tss2_MU_TPM2B_PRIVATE_Unmarshal()

Generates HMAC using TPM.


```c
int32_t Tss2_MU_TPM2B_PRIVATE_Unmarshal(
    const uint8_t *buffer, size_t buffer_size, size_t *offset,
    TPM2B_PRIVATE *out);
```

### Description

This function generates HMAC using TPM. It takes the buffer, buffer size, offset, and TPM2B_PRIVATE as input.

### Parameters

- `data` - pointer to the input data
- `data_length` - length of the input data
- `hmac` - output buffer to save the HMAC
- `hmac_length` - length of the output HMAC buffer hash length
- `tpmHMACPub_key` - File name of the TPM HMAC public key
- `tpmHMACPriv_key` - File name of the TPM HMAC private key

### Return Value

- `0`: If the HMAC generation is successful.
- `-1`: If there is a failure during the HMAC generation process.

<!-- 12 -->
## Tss2_MU_TPM2B_PUBLIC_Unmarshal()

Generates HMAC using TPM.

```c
int32_t Tss2_MU_TPM2B_PUBLIC_Unmarshal(
    const uint8_t *buffer, size_t buffer_size, size_t *offset,
    TPM2B_PUBLIC *out);
```

### Description

This function generates HMAC using TPM. It takes the buffer, buffer size, offset, and TPM2B_PUBLIC as input.

### Parameters

- `data` - pointer to the input data
- `data_length` - length of the input data
- `hmac` - output buffer to save the HMAC
- `hmac_length` - length of the output HMAC buffer hash length
- `tpmHMACPub_key` - File name of the TPM HMAC public key
- `tpmHMACPriv_key` - File name of the TPM HMAC private key

### Return Value

- `0`: If the HMAC generation is successful.
- `-1`: If there is a failure during the HMAC generation process.

## Constants

If the TPM is configured with ECDSA256_DA, then the following constants are defined:

- `TPM_HMAC_PRIV_KEY_CONTEXT_SIZE_128`: 128
- `FDO_TPM2_CURVE_ID`: TPM2_ECC_NIST_P256
- `TPM_AES_BITS`: 128
- `FDO_TPM2_ALG_SHA`: TPM2_ALG_SHA256
- `TPM_HMAC_PRIV_KEY_CONTEXT_SIZE`: 160
- `TPM_HMAC_PUB_KEY_CONTEXT_SIZE`: 48

If the TPM is not configured with ECDSA256_DA, then the following constants are defined:

- `TPM_HMAC_PRIV_KEY_CONTEXT_SIZE_128`: 128
- `FDO_TPM2_CURVE_ID`: TPM2_ECC_NIST_P384
- `TPM_AES_BITS`: 256
- `FDO_TPM2_ALG_SHA`: TPM2_ALG_SHA384
- `TPM_HMAC_PRIV_KEY_CONTEXT_SIZE`: 224
- `TPM_HMAC_PUB_KEY_CONTEXT_SIZE`: 64