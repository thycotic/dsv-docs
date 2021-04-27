[title]: # (Encryption as a Service)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6600)

# Encryption as a Service

DSV offers both a **fully managed** and a **user managed** Encryption as a Service (EaaS). DSV is able to encrypt/decrypt **strings** and **files** under 2MB via the [API](https://dsv.thycotic.com/api#tag/Crypto) or in the CLI using the `crypto` command. The key used for the encryption and decryption is stored as a secret-like object within DSV's architecture. The operations of encrypting and decrypting data are done on-the-fly. Those results are returned to the caller immediately and are not saved within DSV.

## Management Subcommands

**Management** subcommands distinguish whether the encryption key is generated automatically or provided manually.

|Subcommand|Function|
|-|-|
|`auto`| DSV *automatically* generates the encryption key. DSV will default to `auto` if manual is not specified.|
|`manual`| Users *manually* provide the encryption key. Manual must be specified for each input when encrypting with user supplied keys.|

<br>

## Operation Subcommands

**Operation** subcommands act on files and strings.

|Subcommand|Function|Example|
|-|-|-|
|`decrypt` | Function Decrypts a file or string.| `dsv crypto decrypt --path mykeys/key1 --data @file.txt.enc` |
|`encrypt`| Encrypts a file or string.| `dsv crypto encrypt --path mykeys/key1 --data @file.txt` |
|`rotate`| Rotates both data and encryption keys to new versions. **For use with `auto` EaaS only.**| `dsv crypto rotate --path mykeys/key1 --data 'ciphertextstring' --version-start 0`|

<br>

## Key Management Subcommands

**Key Management** subcommands act on encryption keys.

|Subcommands|Function|Example|
|-|-|-|
|`key-create`| Generates a new encryption key. *Used only with managed (`auto`) encryption. Use `key-upload` to supply your own key.*| `dsv crypto key-create --path mykeys/key1`|
|`key-delete`| Mark an encryption key for deletion. The key **and all of its versions** will be removed in about 72 hours. A key that is marked for deletion but not yet removed can be restored using `key-restore`.| `dsv crypto key-delete --path mykeys/key1`|
|`key-read`| Displays the readable data of the encryption key. Reading a `manual` key will show the key and metadata. Reading an `auto` key will display only metadata.| `dsv crypto key-read --path mykeys/key1`|
|`key-restore`| Restores a key that is marked for deletion. Fully removed keys cannot be restored.| `dsv crypto key-restore --path mykeys/key1`|
|`key-update`| Creates a new version of a user supplied encryption key. The `--private-key` flag is required. *For use with `manual` encryption only.* |`dsv crypto manual key-update --path mykeys/key1 --private-key MnI1dTh4L0E/RCHK0...QiY=`|
|`key-upload`| Uploads a new, user supplied, AES256 (symmetric) encryption key to DSV. The `--scheme` and `--private-key` flags are required. **The encryption key must be AES-256, symmetric, base 64 encoded**.| `dsv crypto manual key-upload --path mykeys/key1 --scheme symmetric` <br> `--private-key MnI1dTh4L0E/RchHk0tiUGVTaFZt...QiY= --nonce S1Nze...1Bz`|

<br>

## Flags

**Flags** accompany subcommands to set preferences.

|Flag|Function|Example|
|-|-|-|
|`--data`| Selects the file or string to be encrypted or decrypted| `--data 'secret string'`|
|`--nonce`| Sets the nonce value for `manual` encryption. If omitted, DSV will generate a nonce value.| `--nonce S1Nze...1Bz`|
|`--out`| Sets the output name of a decrypted file.| `--out secret.txt`|
|`--path`| Points to the location of the encryption key.| `--path mykeys/key1`|
|`--scheme`| Sets the scheme for `manual` keys.| `--scheme symmetric`|
|`--version`| Sets the version of the key to use when decrypting data.| `--version 0`|
|`--version-end`| Sets the target key version when reencrypting data.| `--version-end 4`|
|`--version-start`| Sets the current key version to begin rotation.| `--version-start 0`|

<br>

## Encrypting Data

Encrypting data requires three steps:

* Create or Upload an encryption key.
* Encrypt the file or string.
* Decrypt the file or string.

>**NOTE:** Fully-managed encryption uses the `auto` subcommand. When using fully-managed encryption, you do not need to specify `auto` because it is the default for the `crypto` command. When providing your own keys, be sure to use the `manual` subcommand for each input.

### Automatic Key Creation

To create a fully-managed, automatically generated encryption-key:

1. In DSV, create an encryption key using the subcommand and flags: `dsv crypto key-create --path mykeys/key1`. Substitute your own path and key name for `mykeys` and `key1`.
1. The CLI returns a confirmation of key creation. This metadata can also be read using the `dsv crypto key-read --path mykeys/key1` command:
    ```json
    {
    "created": "2021-03-01T19:12:58z",
     "createdBy": "users:thy-one:your.username@organization.com",
     "id": "identificationstring",
     "lastModified": "2021-03-01T19:12:58z",
     "lastModifiedBy": "users:thy-one:your.username@organization.com",
     "path": "mykeys:key1",
     "version": "0"
    }
    ```

### Manual Key Creation

To upload your own encryption key:

1. In DSV, upload an encryption key using the subcommand and flags: `dsv crypto manual key-upload --path mykeys/key1 --scheme symmetric --private-key MnI1dTh4L0E/RchHk0tiUGVTaFZt...QiY= --nonce S1Nze...1Bz`. **The `private-key` that you supply must be AES 256, symmetric, 64 bit encoded. The `scheme` value must be "symmetric".** If the `nonce` value is omitted, DSV will generate it for you.
1. The CLI returns a confirmation of key upload. This data can also be read using the `dsv crypto manual key-read --path mykeys/key1` command:
    ```json
    {
    "created": "2021-03-01T19:12:58z",
     "createdBy": "users:thy-one:your.username@organization.com",
     "data": {
        "metadata": null,
        "nonce": "S1Nze...1Bz",
        "privateKey": "MnI1dTh4L0E/RchHk0tiUGVTaFZt...QiY=",
        "scheme": "symmetric"
    },
     "description": "",
     "id": "identificationstring",
     "lastModified": "2021-03-01T19:12:58z",
     "lastModifiedBy": "users:thy-one:your.username@organization.com",
     "path": "mykeys:key1",
     "version": "0"
    }
    ```

### String Encryption

After creating or uploading an encryption key, follow these steps to encrypt a string. If you are using a manually supplied key, be sure to include the `manual` subcommand after the `crypto` command in the examples.

1. **Encrypt** the string using the `dsv crypto encrypt` subcommand along with the encryption key `--path` and the string `--data`:
    ```
    dsv crypto encrypt --path mykeys/key1 --data 'Example String'
    ```
1. The CLI returns a confirmation of encryption:
    ```json
    {
    "ciphertext": "zIPFkidTB51...cz2CEZ4+n",
    "path": "mykeys/key1",
    "version": "0"
    }
    ```
1. Make sure you **save the ciphertext string and version**. You will need that information when attempting to decrypt in the future.
1. **Decrypt** the string using the `dsv crypto decrypt` subcommand along with the same encryption key `--path` and the ciphertext as the `--data` value:
    ```
    dsv crypto decrypt --path mykeys/key1 --data 'zIPFkidTB51...cz2CEZ4+n'
    ```
1. the CLI returns the value of the decrypted string:
    ```json
    {
    "data": "Example String",
    "path": "mykeys/key1",
    "version": "0"
    }
    ```

### File Encryption

After creating or uploading an encryption key, follow these steps to encrypt a file. If you are using a manually supplied key, be sure to include the `manual` subcommand after the `crypto` command in the examples.

>**NOTE**: The maximum file size is 2MB including overhead associated with DSV encoding and transporting.

1. **Encrypt** the file using the `dsv crypto encrypt` subcommand along with the encryption key `--path` and the `--data` flag pointing to the file location. (*Optional*) Give the encrypted file a new name using the `--out` flag. If no new filename is specified, DSV will append **.enc** to the original filename.
    ```
    dsv crypto encrypt --path mykeys/key1 --data @file.txt
    ```
1. DSV saves the encrypted file. The CLI returns a confirmation of encryption:
    ```
    Ciphertext with metadata successfully saved in file.txt.enc
    ```
1. **Decrypt** the file using the `dsv crypto decrypt` subcommand along with the same encryption key `--path` and the new .enc file as the `--data` value. (*Optional*) Give the decrypted file a new name using the `--out` flag. If no new filename is specified, DSV will append **.txt** to the file name. :
    ```
    dsv crypto decrypt --path mykeys/key1 --data @file.txt.enc --out decryptedfile.decrypted
    ```
1. DSV decrypts and saves the file. The CLI returns confirmation of decryption:
    ```
    Decrypted data with metadata successfully saved in decryptedfile.decrypted
    ```
The decrypted file will contain the metadata associated with the original encrypted file (i.e. version, path, and data). The data value remains base64 encoded. If you want to obtain the original file, you will need to base64 decode the data value:
```bash
# Linux -- prerequisites: jq, base64, md5sum
> jq -r '.data' decryptedfile.decrypted | base64 -d > decryptedfile.original
> md5sum file.txt decryptedfile.original
adbb83c57dc433b3a1d0e887ea3c029f  file.txt
adbb83c57dc433b3a1d0e887ea3c029f  decryptedfile.original
```

## Key Rotation and Versioning

For fully-managed (`auto`) encryption, both keys and data can be rotated. **A new version of a key can only be created by rotating data**. When data is rotated, it is decrypted using the original encryption key, and reencrypted with the new version. 

>**Note**: The original version of a key is designated as **Version 0**.

### Creating a New Key Version

A new key version is created automatically when encrypted data is rotated *using the most recent version of the key.* To rotate an encryption key and data to a new version:

1. Use the rotate subcommand along with
    * the `--path` to the key to be rotated.
    * the already encrypted data (ciphertext or file) from the previous version as the value for `--data`.
    * the current **version number** of the data as the value for `--version-start`.
    * (Optional) For files, the `--out` flag can be used to specify the name of the reencrypted file.

    ```
    dsv crypto rotate --path mykeys/key1 --data 'zIPFkidTB51...cz2CEZ4+n' --version-start 0
    ```
1. The data is now re-encrypted as version 1, and key version 1 has been created.
    
    >**Note:** If version 0 of the ciphertext/file is saved, it can still be decrypted using version 0 of the key. The newly returned version 1 ciphertext/file can only be decrypted using version 1 of the key.

1. The CLI returns a confirmation of rotation. 

    ```json
    {
    "ciphertext": "pcrvO6gXy0a...k9RKKHV9n",
    "path": "mykeys/key1",
    "version": "1"
    }
    ```

1. The string or file can now be decrypted by passing the new `--data` value along with the `--version` number. If no `--version` is set, DSV will default to the most recent version of the key.
    ```
    dsv crypto decrypt --path mykeys/key1 --data 'pcrvO6gXy0a...k9RKKHV9n' --version 1
    ```

### Rotating to an Existing Key Version

To rotate data to an existing version of a key:

1. Use the rotate subcommand along with
    * the `--path` to the key.
    * the already encrypted data (ciphertext or file) from the previous version as the value for `--data`.
    * the **version number** of the key with which the data was previously encrypted as the value for `--version-start`.
    * the new version of the key to use for encryption as the value for `--version-end`.
1. This example input will rotate the file from version 3 of the key to version 6:

    ```
    dsv crypto rotate --path mykeys/key1 --data @passwordv3.enc --version-start 3 --version-end 6 --out @passwordv6.enc
    ```
1. The CLI returns a confirmation of data rotation:
    ```json
    {
    "file": "@passwordv6.enc",
    "path": "mykeys/key1",
    "version": "6"
    }
    ```
1. The new file can now be decrypted using version 6 of the key.

## Manual Key Updating

For user supplied (`manual`) encryption, key values can be updated. Note that the original version of a key is designated as version 0.

To update a key:

1. Use the `key-update` subcommand along with
    * the `--path` to the existing key
    * the new key as the value for `--private-key`.
    * (optional) a new `--nonce` string.
    * **Example**: `dsv crypto manual key-update --path mykeys/key1 --private key MnI1dTh4L0E/RchHk0tiUGVTaFZt...QiY=`
1. The CLI returns a confirmation of the key update. Note that the newly updated key is now designated as version 1:
    ```json
    {
     "attributes": null,
     "created": "2021-03-01T19:12:58z",
     "createdBy": "users:thy-one:your.username@organization.com",
     "data": {
        "metadata": null,
        "nonce": "S1Nze...1Bz",
        "privateKey": "MnI1dTh4L0E/RchHk0tiUGVTaFZt...QiY=",
        "scheme": "symmetric"
    },
     "description": "",
     "id": "identificationstring",
     "lastModified": "2021-03-01T19:12:58z",
     "lastModifiedBy": "users:thy-one:your.username@organization.com",
     "path": "mykeys:key1",
     "version": "1"
    }
    ```
1. All encrypted files or strings must be decrypted with the key **`--version`** that was used for encryption. DSV defaults to using the most recent version unless a version is specified.