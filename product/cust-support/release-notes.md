[title]: # (Release Notes)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2110)

# Release Notes

Thycotic periodically updates DevOps Secrets Vault, such as to provide fixes and improvements or to introduce additional features. As a cloud application, DSV lacks version numbers, because updates become available to all users as they occur—the current version is always the only version available.

* However, users operate DSV through a Command Line Interface (CLI) provided by downloaded, locally installed, and OS-specific executables. These bear version numbers.

* Thycotic periodically updates these OS-specific executables to deliver fixes, improvements, and feature additions, as needed to keep the customer-side CLI executables conformant with the cloud-based DSV service offerings.

* The version number will always be the same across the OS editions of the CLI executable; if a change resulting in a version number iteration only affects one of the OS editions, it will be noted, but all editions will still get the new version number.

* You obtain these updated versions of the CLI executables by downloading them from [DevOps Secrets Vault Downloads](https://dsv.thycotic.com/downloads).

* Generally, older versions of CLI executables will continue to work, but you will want to have the latest executables to benefit from fixes and obtain new features.

This article tracks changes to DSV. Highlights of the most recent update appear first. The same information appears in the tables that follow. The first table covers the cloud basis for DSV, and the second covers the OS-specific CLI executables and the API. In both tables, the most recent changes appear first.

## October 2019 Release Notes Highlights

With the update command’s **new flags**, *--data*, *-- desc*, and *--attributes*, you can update each of the three parts of a secret separately from the other parts. 

The secret update command also has a **new** *--overwrite* flag. This Boolean flag controls whether the *--data* flag’s content overwrites extant fields in the secret’s data object, or merges with them.

Related to those improvements, a secret’s data section may now have **as many fields as you specify**.

**Caching behavior** has been updated. The change provides better handling of situations where two or more users are caching information about a secret and choose to make updates.

You can now **find and examine audit logs via the CLI**. Previously, this was only possible through the API. 

The new *restore* command allows you to retrieve a deleted secret, role, user, or group up to 72 hours after deleting it, which places DSV by default in a soft delete posture. However, you can preclude any restore operation by using the *delete* command’s *--force* flag.

## September 2019 Release Notes Highlights

**Configuration now scales more effectively**, with policies and authentication providers residing in separate files to allow for independent updates. Before, a single configuration file held all policies and authentication providers.

This release **omits the `permissions` command because the `policy` command supersedes it**—named policies no longer require everyone to modify a global document. 

To fix a bug in the API Audit Search function, **the `secret` parameter in Audit Search is now the `path` parameter**.

A **new Change Password feature** enables users to change their passwords.

**Adding users to a group achieves permissions delegation** in this release.

**Deleting a secret now deletes all past versions**, rather than just the latest.

## DSV Cloud Service: Change Log

| **Update**             | **Notes**                                  |
|------------------------|--------------------------------------------|
| October 2019           | improvement: a secret’s data, attributes, and description can be individually updated via the update command’s new --data, --attributes and --desc flags, respectively |
|                        | improvement: the secret update command’s new Boolean --overwrite flag controls whether the --data flag’s content overwrites or merges with extant data object fields |
|                        | improvement: a secret’s data section may now have as many fields as an organization requires |
|                        | improvement: updated caching behavior improves outcomes where two or more users with cached secret information choose to make updates  |
|                        | improvement: the CLI now supports finding and examining audit logs, previously possible only via the API |
|                        | improvement: the new restore command allows a deleted secret, role, user, or group to be retrieved up to 72 hours after deletion, excepting when the delete flag --force was used  |
|                        |      |
| September 2019         | improvement: better scaling of configuration files achieved by keeping policies and authentication providers in separate files  |
|                        | improvement: the `permissions` command has been superseded by the `policy` command; named policies no longer require everyone to modify a global document |
|                        | improvement: the new Change Password feature enables users to change their passwords |
|                        | improvement: adding users to a group achieves permissions delegation |
|                        | improvement: deleting a secret now deletes all past versions, rather than just the latest |
|                        | fixed: the API Audit Search function’s bug, related to the improperly named `secret` parameter, is resolved by the properly named `path` parameter |
|                        |      |
| August 2019            | fixed: issue where the refresh token generated by Thycotic One authentication was not correctly generating the full subject name and could cause access denied errors |
|                        | fixed: issue where adding a pre-existing Thycotic One user as a DSV user would not correctly save the Thycotic One user id |
|                        | fixed: issue where the config created and updated metadata fields that were not properly shown in responses |
|                        | added: version validation to **config update** to help prevent conflicts |
|                        |      |
| July 2019              | first General Availability of the service  |
 
  
  
 
## DSV CLI Executables for Windows, Linux, and MacOS: Version History

| **Version** | **Date**   | **Notes**  |
|-------------|------------|------------|
| 1.0.1       | 2019.08.15 | fixed: issue where the CLI returned an error when the stored refresh token had been invalidated and the current cached access token was expired |
|             |            | improved: when you upload a config document from a file via the **config update** command, the updated config from the cloud is now saved back to the local file; this ensures the return to the user of any auto-generated IDs for the permission policies and settings |
|             |            | fixed: initialization issues with Windows Credential Manager and Linux Pass storage |
| 1.0.0       | 2019.07.23 | version coinciding with the July 2019 first General Availability of the DSV service |

