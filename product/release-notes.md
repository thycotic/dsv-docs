[title]: # (Release Notes)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (8000)

# Release Notes

Thycotic periodically updates DevOps Secrets Vault to provide fixes and improvements and introduce features.

As a Cloud application, DSV lacks version numbers; the current version serves all users because it is always the only version available.

The Command Line Interface (CLI) is locally installed using OS-specific executables. These bear version numbers to reflect updates.
* The version number will always be the same across the OS-specific editions of the CLI executable.
* You obtain these updated versions of the CLI executables by downloading them from [DevOps Secrets Vault Downloads](https://dsv.thycotic.com/downloads). 
* The CLI itself will notify you when a new version is available for download.
* Generally, older versions of CLI executables will continue to work, but you will want to have the latest executables to benefit from fixes and obtain new features.


## DSV Cloud Service: Change Log

| **Update**             | **Notes**                                  |
|------------------------|--------------------------------------------|
|May 2020                |**new feature** Google Cloud Platform (GCP) Dynamic secrets.  DSV can issue ephemeral secrets for GCP service accounts|
|                        |**new feature** OIDC Support.  Thycotic One can connect to any IDP provider that supports OIDC and in-turn those users can authenticate to DSV.|
|                        |**improvement** If a base secret has a dynamic secret linked to it, it errors on attempt to delete it.|
|                        |**improvement** New flag for singing a leaf certificate that includes the singing certificate for the trust chain| 
|                        |**fixed      ** Groups with 3rd party auth fixed|
|                        |**fixed      ** Client permission check|
|                        |**fixed      ** Restore user with 3rd party auth|
|April 2020              |**new feature** Google Cloud Platform Authentication using service accounts and GCE metadata|
|                        |**new feature** X.509 Certificate Issuance.  Certificate signing capablilties.
|                        |**improvement**: Azure dynamic secret role validation  |
|                        |**improvement**: Azure dynamic secret temporary service principal cleanup. (deletes expired service principals in Azure MSI) |
|                        |**improvement**: Dynamic secrets easier to edit     |
|                        |**fixed** CLI encryption key works if store path is in a non-default location.    |
|                        |**fixed** Client tokens used even if already logged in.     |
|March 2020              | **new feature**: Azure Dynamic Secrets.  DSV can use Azure Service Principals to provide ephermal credentials|
|                        | **new feature**: (API only) Ability to issue X.509 certificates
|                        | **improvement**: Ability to retrieve auth settings by version          |
|                        | **improvement**: Make help commmands available even if teh CLI config is missing
|                        | **improvement**: Protect error check.  Protect against creating policy errors   |
|                        | **improvement**: Ability to search for dynamic secrets given a base secret        |
|                        | **improvement**: Improved error reporting for dynamic secrets         
|                        | **fixed      **: A malformed policy could prevent reading all policies.      |
|                        | 
|February 2020           | **improvement**: protect against user lockout. When editing authentication providers, block any changes that locks the user out of the account. |
|                        | **improvement**: audit search results now inclusive of the dates in a range (previously the first day was omitted). |
|                        | **improvement**: consistent version listing. Removed the “v” in the version number when searching older versions to be consistent with other listings. |
|                        | **new feature**: AWS Dynamic secrets. DevOps secrets Vault can use AWS Security Token Service (STS) to provide ephemeral AWS credentials. |
| January 2020           | **improvement**: the `rollback` command allows you to roll back Secrets (and Policies and Authentication Providers) to their earlier versions  |
|                        | **improvement**: Windows users can now more easily edit Secrets, with Notepad or another designated editor opening right from the command line |
|                        | **fixed**: a defect in the Kubernetes extension caused verbose error reporting on irrelevant conditions |
|                        |      |
| December 2019          | **improvement**: the `thy init` command no longer requires an `--advanced` flag, as it now always steps through key initialization settings |
|                        | **improvement**: the DSV CLI executables will now prompt when a new version is available for download |
|                        | **fixed**: a defect in CLI audit log listing behavior would show listings even when the start date was in the future and would show listings later than the end date |
|                        |      |
| November 2019          | **improvement**: after deleting a Secret, Role, User, Group, Policy, or Authentication Provider, the new `restore` command will undelete the item up to 72 hours later
|                        | **improvement**: architectural changes back uptime of 99.999 percent; continuous backup enables hot backup fail-over in under a minute |
|                        |      |
| October 2019           | **improvement**: a Secret’s data, attributes, and description can be individually updated via the *update* command’s new *--data*, *--attributes* and *--desc* flags, respectively |
|                        | **improvement**: the Secret *update* command’s new Boolean *--overwrite* flag controls whether the *--data* flag’s content overwrites or merges with extant data object fields |
|                        | **improvement**: improvement: updated server side policy caching to better handle permission updates |
|                        | **improvement**: the CLI now supports finding and examining audit logs, previously possible only via the API |
|                        |      |
| September 2019         | **improvement**: better scaling of configuration files achieved by keeping policies and authentication providers in separate files  |
|                        | **improvement**: the *permissions* command has been superseded by the *policy* command; named policies no longer require everyone to modify a global document |
|                        | **improvement**: the new Change Password feature enables users to change their passwords |
|                        | **improvement**: adding Users to a Group achieves permissions delegation |
|                        | **improvement**: deleting a Secret now deletes all past versions, rather than just the latest |
|                        | **fixed**: the API Audit Search function’s bug, related to the improperly named *Secret* parameter, is resolved by the properly named *path* parameter |
|                        |      |
| August 2019            | **fixed**: issue where the refresh token generated by Thycotic One authentication was not correctly generating the full subject name and could cause access denied errors |
|                        | **fixed**: issue where adding a pre-existing Thycotic One user as a DSV User would not correctly save the Thycotic One user id |
|                        | **fixed**: issue where the config created and updated metadata fields that were not properly shown in responses |
|                        | **added**: version validation to *config update* to help prevent conflicts |
|                        |      |
| July 2019              | first General Availability of the service  |
 

## DSV CLI Executables for Windows, Linux, and macOS: Version History

| **Version** | **Date**   | **Notes**  |
|-------------|------------|------------|
| 1.0.1       | 2019.08.15 | **fixed**: issue where the CLI returned an error when the stored refresh token had been invalidated and the current cached access token was expired |
|             |            | **improved**: when you upload a config document from a file via the *config update* command, the updated config from the Cloud is now saved back to the local file; this ensures the return to the User of any auto-generated IDs for the permission policies and settings |
|             |            | **fixed**: initialization issues with Windows Credential Manager and Linux Pass storage |
| 1.0.0       | 2019.07.23 | first General Availability of the service |

