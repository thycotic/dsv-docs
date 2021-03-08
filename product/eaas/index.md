[title]: # (Encryption as a Service)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6600)

# Encryption as a Service

DSV offers **fully managed** Encryption as a Service (EaaS). DSV can encrypt/decrypt **strings** and **files** in the CLI using the `crypto` command.

|Subcommands|Function|Example|
|-|-|-|
|`decrypt`| Decrypts a file or string.| `dsv crypto decrypt --path mykeyfolders/key1 --data @file.txt.enc` |
|`encrypt`| Encrypts a file or string.| `dsv crypto encrypt --path mykeyfolders/key1 --data @file.txt` |
|`key-create`| Creates a new encryption key.| `dsv crypto key-create --path mykeyfolder/key1`|
|`key-delete`| Mark an encryption key for deletion. The key **and all of its versions** will be removed in about 72 hours. A key that is marked for deletion but not yet removed can be restored using `key-restore`.| `dsv crypto key-delete --path mykeyfolder/key1`|
|`key-read`| Shows the metadata of the encryption key. As keys are currently **fully managed** by DSV, the actual encryption key cannot be read.| `dsv crypto key-read --path mykeyfolder/key1`|
|`key-restore`| Restores a key that is marked for deletion. Fully removed keys cannot be restored.| `dsv crypto key-restore --path mykeyfolder/key1`|
|`rotate`| Rotate an encryption key to a new version.| `dsv crypto rotate --path mykeyfolder/key1 --data 'ciphertextstring' --version-start 0`|

<br>

|Flags|Function|Example|
|-|-|-|
|`--data`| Selects the file or string to be encrypted or decrypted| `--data 'secret string'`|
|`--out`| Sets the output name of a decrypted file.| `--out secret.txt`|
|`--path`| Points to the location of the encryption key| `--path mykeyfolder/key1`|
|`--version-end`| 
|`--version-start`| Sets the key version to begin rotation.| `--version-start 0`|

<br>

## Encrypting Data

Encrypting data requires three steps:

* Create an encryption key.
* Encrypt the file or string.
* Decrypt the file or string.

### Encrypting a String

1. In DSV, create an encryption key using the subcommand and flags `dsv crypto key-create --path mykeyfolder/key1`. Substitute your own folder and key name for `mykeyfolder` and `key1`.
1. The CLI returns a confirmation of key creation. This metadata can also be read using the `dsv crypto key-read --path mykeyfolder/key1` command:
    ```json
    {"created": "2021-03-01T19:12:58z",
     "createdBy": "users:thy-one:your.username@organization.com",
     "id": "identificationstring",
     "lastModified": "2021-03-01T19:12:58z",
     "lastModifiedBy": "users:thy-one:your.username@organization.com",
     "path": "mykeyfolder:key1",
     "version": "0"
    }
    ```
1. **Encrypt** the string using the `dsv crypto encrypt` subcommand along with the encryption key `--path` and the string `--data`:
    ```
    dsv crypto encrypt --path mykeyfolder/key1 --data 'Example String'
    ```
1. The CLI returns a confirmation of encryption:
    ```json
    {
    "ciphertext": "ciphertextstring",
    "path": "mykeyfolder/key1",
    "version": "0"
    }
    ```
1. Make sure to **save the ciphertext string** to use when decrypting the data.
1. **Decrypt** the string using the `dsv crypto decrypt` subcommand along with the same encryption key `--path` and the ciphertext as the `--data` value:
    ```
    dsv crypto decrypt --path mykeyfolders/key1 --data 'ciphertextstring'
    ```
1. the CLI returns the value of the decrypted string:
    ```json
    {
    "data": "Example String",
    "path": "mykeyfolder/key1",
    "version": "0"
    }
    ```

### Encrypting a File

1. In DSV, create an encryption key using the subcommand and flags `dsv crypto key-create --path mykeyfolder/key1`. Substitute your own folder and key name for `mykeyfolder` and `key1`.
1. The CLI returns a confirmation of key creation. This metadata can also be read using the `dsv crypto key-read --path mykeyfolder/key1` command:
    ```json
    {"created": "2021-03-01T19:12:58z",
     "createdBy": "users:thy-one:your.username@organization.com",
     "id": "identificationstring",
     "lastModified": "2021-03-01T19:12:58z",
     "lastModifiedBy": "users:thy-one:your.username@organization.com",
     "path": "mykeyfolder:key1",
     "version": "0"
    }
    ```
1. **Encrypt** the file using the `dsv crypto encrypt` subcommand along with the encryption key `--path` and the `--data` flag pointing to the file location.
    ```
    dsv crypto encrypt --path mykeyfolder/key1 --data @file.txt
    ```
1. DSV saves the encrypted file and appends **.enc** to the filename. The CLI returns a confirmation of encryption:
    ```
    Ciphertext with metadata successfully saved in file.txt.enc
    ```
1. **Decrypt** the file using the `dsv crypto decrypt` subcommand along with the same encryption key `--path` and the new .enc file as the `--data` value. (*Optional*) Give the decrypted file a new name using the `--out` flag.:
    ```
    dsv crypto decrypt --path mykeyfolders/key1 --data @file.txt.enc --out decryptedfile.txt
    ```
1. DSV decrypts and saves the file. If no `--out` value is given, DSV will append **.txt** to the file name. The CLI returns confirmation of decryption:
    ```
    Decrypted data with metadata successfully saved in decryptedfile.txt
    ```

## Key Rotation and Versioning

To rotate an encryption key to a new version:

1. Use the rotate subcommand along with
    * the `--path` to the key to be rotated.
    * the encrypted data ciphertext from the previous version as the value for `--data`.
    * the current **version number** as the value for `--version-start`.
    ```
    dsv crypto rotate --path mykeyfolder/key1 --data 'ciphertextstring' --version-start 0
    ```
1. The CLI returns a confirmation of key rotation along with the new **ciphertext** for the encrypted data. Be sure to save this value for later decryption or rotation.
    ```json
    {
    "ciphertext": "newciphertextstring",
    "path": "mykeyfolder/key1",
    "version": "1"
    }
    ```
1. The data is now re-encrypted and a new key version is created.
1. The string can now be decrypted by passing the new ciphertext as `--data` along with the `--version` number.
    ```
    dsv crypto decrypt --path mykeyfolder/key1 --data 'newciphertextstring' --version 1
    ```
