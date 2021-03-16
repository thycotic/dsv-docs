[title]: # (Encryption as a Service)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6600)

# Encryption as a Service

DSV offers **fully managed** Encryption as a Service (EaaS). DSV can encrypt/decrypt **strings** and **files** in the CLI using the `crypto` command.

## Operation Subcommands

**Operation** subcommands act on files and strings.

|Subcommand|Function|Example|
|-|-|-|
|`decrypt`| Decrypts a file or string.| `dsv crypto decrypt --path mykeys/key1 --data @file.txt.enc` |
|`encrypt`| Encrypts a file or string.| `dsv crypto encrypt --path mykeys/key1 --data @file.txt` |
|`rotate`| Rotates both data and encryption keys to new versions.| `dsv crypto rotate --path mykeys/key1 --data 'ciphertextstring' --version-start 0`|

<br>

## Key Management Subcommands

**Key Management** subcommands act on encryption keys.

|Subcommands|Function|Example|
|-|-|-|
|`key-create`| Creates a new encryption key.| `dsv crypto key-create --path mykeys/key1`|
|`key-delete`| Mark an encryption key for deletion. The key **and all of its versions** will be removed in about 72 hours. A key that is marked for deletion but not yet removed can be restored using `key-restore`.| `dsv crypto key-delete --path mykeys/key1`|
|`key-read`| Shows the metadata of the encryption key. As keys are currently **fully managed** by DSV, the actual encryption key cannot be read.| `dsv crypto key-read --path mykeys/key1`|
|`key-restore`| Restores a key that is marked for deletion. Fully removed keys cannot be restored.| `dsv crypto key-restore --path mykeys/key1`|

<br>

## Flags

**Flags** accompany subcommands to set preferences.

|Flag|Function|Example|
|-|-|-|
|`--data`| Selects the file or string to be encrypted or decrypted| `--data 'secret string'`|
|`--out`| Sets the output name of a decrypted file.| `--out secret.txt`|
|`--path`| Points to the location of the encryption key.| `--path mykeys/key1`|
|`--version`| Sets the version of the key to use when decrypting data.| `--version 0`|
|`--version-end`| Sets the target key version when reencrypting data.| `--version-end 4`|
|`--version-start`| Sets the current key version to begin rotation.| `--version-start 0`|

<br>

## Encrypting Data

Encrypting data requires three steps:

* Create an encryption key.
* Encrypt the file or string.
* Decrypt the file or string.

### Encrypting a String

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
1. **Encrypt** the string using the `dsv crypto encrypt` subcommand along with the encryption key `--path` and the string `--data`:
    ```
    dsv crypto encrypt --path mykeys/key1 --data 'Example String'
    ```
1. The CLI returns a confirmation of encryption:
    ```json
    {
    "ciphertext": "ciphertextstring",
    "path": "mykeys/key1",
    "version": "0"
    }
    ```
1. Make sure to **save the ciphertext string** to use when decrypting the data.
1. **Decrypt** the string using the `dsv crypto decrypt` subcommand along with the same encryption key `--path` and the ciphertext as the `--data` value:
    ```
    dsv crypto decrypt --path mykeys/key1 --data 'ciphertextstring'
    ```
1. the CLI returns the value of the decrypted string:
    ```json
    {
    "data": "Example String",
    "path": "mykeys/key1",
    "version": "0"
    }
    ```

### Encrypting a File

1. In DSV, create an encryption key using the subcommand and flags `dsv crypto key-create --path mykeys/key1`. Substitute your own path and key name for `mykeys` and `key1`.
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
    dsv crypto decrypt --path mykeys/key1 --data @file.txt.enc --out decryptedfile.txt
    ```
1. DSV decrypts and saves the file. The CLI returns confirmation of decryption:
    ```
    Decrypted data with metadata successfully saved in decryptedfile.txt
    ```

## Key Rotation and Versioning

Both keys and data can be rotated. **A new version of a key can only be created by rotating data**. When data is rotated, it is decrypted using the original encryption key, and reencrypted with the new version. 

>**Note**: The original version of a key is designated as **Version 0**.

### Creating a New Key Version

A new key version is created automatically when encrypted data is rotated *using the most recent version of the key.* To rotate an encryption key and data to a new version:

1. Use the rotate subcommand along with
    * the `--path` to the key to be rotated.
    * the already encrypted data (ciphertext or file) from the previous version as the value for `--data`.
    * the current **version number** of the data as the value for `--version-start`.
    * (Optional) For files, the `--out` flag can be used to specify the name of the reencrypted file.

    ```
    dsv crypto rotate --path mykeys/key1 --data 'ciphertextstring' --version-start 0
    ```
1. The data is now re-encrypted as version 1, and key version 1 has been created.
    
    >**Note:** If version 0 of the ciphertext/file is saved, it can still be decrypted using version 0 of the key. The newly returned version 1 ciphertext/file can only be decrypted using version 1 of the key.

1. The CLI returns a confirmation of rotation. 

    ```json
    {
    "ciphertext": "newciphertextstring",
    "path": "mykeys/key1",
    "version": "1"
    }
    ```

1. The string or file can now be decrypted by passing the new `--data` value along with the `--version` number. If no `--version` is set, DSV will default to the most recent version of the key.
    ```
    dsv crypto decrypt --path mykeys/key1 --data 'newciphertextstring' --version 1
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
1. 
    ```json
    {
    "file": "@passwordv6.enc",
    "path": "mykeys/key1",
    "version": "6"
    }
    ```
1. The new file can now be decrypted using version 6 of the key.