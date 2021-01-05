[title]: # (Release Notes)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (9000)

# Release Notes

DevOps Secrets Vault is regularly updated to provide improvements and introduce features.

As a Cloud application, DSV lacks version numbers; the current version serves all users because it is always the only version available.

The Command Line Interface (CLI) is locally installed using OS-specific executables. These bear version numbers to reflect updates.
* The version number will always be the same across the OS-specific editions of the CLI executable.
* You obtain these updated versions of the CLI executables by downloading them from [DevOps Secrets Vault Downloads](https://dsv.thycotic.com/downloads). 
* The CLI itself will notify you when a new version is available for download.
* Generally, older versions of CLI executables will continue to work, but you will want to have the latest executables to benefit from fixes and obtain new features.


## DSV Cloud Service: Change Log

| **Update**             | **Notes**                                  |
|------------------------|--------------------------------------------|
|January 2021            |**CLI Version**: 1.17.0|
|                        |**Engine Version**: 1.3|
|                        |**new feature**: Added `sendWelcomeEmail` property. When a user is created using Thycotic One for authentication, setting `sendWelcomeEmail` to `true` will send a new login email to the user.|
|                        |**new feature**: Added ability to modify authentication provider details using the `edit` subcommand.|
|                        |**improvement**: After account lockout from failed authentication, the CLI now displays the time remaining until reauthentication is available.|
|                        |**improvement**: Updated dsv-engine validation and API error messages for clarity.|
|                        |**fixed**: When querying log data, the correct dates will display.|
|                        |**fixed**: Client credential `URL` value no longer switches with search.|
|                        |**fixed**: External ID is no longer required for Thycotic One users.|
|December 2020           |**CLI Version**: 1.16.0|
|                        |**Engine Version**: 1.2|
|                        |**new feature**: Added dynamic secret support for contained MSSQL databases.|
|                        |**new feature**: Added ephemeral client credentials. Credentials can be limited using the `--uses` and `--ttl` flags.|
|                        |**improvement**: Passwords can no longer be reused, increasing security.|
|                        |**improvement**: DSV APIs now limit the number of invalid login attempts, increasing security.|
|                        |**improvement**: Azure dynamic secrets now use consistent naming conventions between the base and dynamic secret.|
|                        |**improvement**: strictTransportHeader is present in requests.|
|November 2020           |**CLI Version**: 1.15.0                      |
|                        |**Engine Version**: 1.1                     |
|                        |**new feature**: Added dynamic secret support for PostgreSQL and Oracle databases.|
|                        |**improvement**: Engines and pools can now be manipulated via the `engine` and `pool` commands.|
|October 2020            | **CLI Version**: 1.14|
|                        | **Engine Version**: 1.0|
|                        | **new feature**: Added the DSV Engine.  This agent is installed on the customer network for access while limiting the need to open the firewall.  Initially for database dynamic secrets, but in the future will be used for password rotation, authentication, or other needs.|
|                        | **new feature**: Bootstrapped client credentials.  When creating client credentials, a one-time use URL can be created so that the new machine or application can retrieve the Client Secret.|
|September 2020          | **CLI Version**: 1.13|
|                        | **improvement**: CLI name changed from "thy" to "dsv" in downloads and documentation for all commands|
|                        | **new feature**: Home Vault GA.  Completed Roles, GetByVersion, Rollback, Restore, Policy for giving others access. |
|                        | **improvement**: Wizards for Groups will not allow invalid Users|
|                        | **improvement**: Wizards for Users look for Auth provider and act accordingly rather than ask for a password first|
|                        | **improvement**: secrets attributes can be updated without affecting other fields|
|                        | **improvement**: Thycotic One users not sent sign-up emails by default.  Can change this setting|
|                        | **improvement**: whoami command provides more information for cloud auth providers|
|                        | **improvement**: Group names can't have spaces|
|                        | **improvement**: Roles with Auth providers must include an external ID|
|August 2020 (Update 1)  | **CLI Version**: 1.12.1                    |
|                        | **fixed**: CLI update check                |
|August 2020             | **CLI Version**: 1.12|
|                        | **new feature**: Home Vault Beta.  Users get their own secret space without needing a policy.|
|                        | **improvement**: Global flags defined|
|                        | **improvement**: Policy update help information and examples.|
|                        | **improvement**: Improved auth-provider help|
|                        | **improvement**: Pre-validation for SIEM endpoints|
|                        | **fixed**: Added Metadata to Groups|
|July 2020 (Update 1)    | **fixed**: Enforce case insensitivity on subjects returned in JWT record.|
|July 2020               | **CLI Version**: 1.11|
|                        | **new feature**: SSH public key generation and SSH Certificate signing/storage was added.|
|                        | **new feature**: CLI now contains wizards for Users, Groups, and Roles.|
|                        | **improvement**: Policy update help information and examples.|
|                        | **improvement**: Added IDs and status information to audit records.|
|                        | **improvement**: Standardized on the use of colons for policies instead of slashes|
|                        | **fixed**: Enhancements to auth providers.|
|                        | **fixed**: Group memberships are not evaluated for policy updates.|
|                        | **fixed**: Group member sometimes returned code 500 (internal server error) on deletion attempt.|
|June 2020               | **CLI Version**: 1.10|
|                        | **new feature**: SIEM endpoints.  Support Syslog, CEF, and JSON log formatting on TLS,TCP, UDP, HTTP, and HTTPS transport protocols.|
|                        | **new feature**: Introduced CLI wizards to PKI, SIEM, Policy, and Auth-provider commands for simplified human navigation.|
|                        | **improvement**: Additional Secrets search capabilities. Enabled search for Secrets on any attribute, path, or description.|
|                        | **improvement**: Provide the ability to add a CRL URL to a signing certificate.  |
|                        | **fixed**: CLI version check fixed regardless of the update cache|
|                        | **fixed**: Group membership evaluated for policy updates. |
|                        | **update**: Deprecated "settings" attribute on the Configuration document will be removed next release. All auth provider management should go through the config/auth endpoint |
|May 2020                | **CLI Version**: 1.9|
|                        | **new feature**: Google Cloud Platform (GCP) Dynamic secrets.  DSV can issue ephemeral secrets for GCP service accounts|
|                        | **new feature**: OIDC Support.  Thycotic One can connect to any IDP provider that supports OIDC and in-turn those users can authenticate to DSV.|
|                        | **improvement**: If a base secret has a dynamic secret linked to it, it errors on attempt to delete it.|
|                        | **improvement**: New flag for singing a leaf certificate that includes the singing certificate for the trust chain| 
|                        | **fixed**: Groups with 3rd party auth fixed|
|                        | **fixed**: Client permission check|
|                        | **fixed**: Restore user with 3rd party auth|
|April 2020              | **CLI Version**: 1.8|
|                        | **new feature**: Google Cloud Platform Authentication using service accounts and GCE metadata|
|                        | **new feature**: X.509 Certificate Issuance.  Certificate signing capablilties.
|                        | **improvement**: Azure dynamic secret role validation  |
|                        | **improvement**: Azure dynamic secret temporary service principal cleanup. (deletes expired service principals in Azure MSI) |
|                        | **improvement**: Dynamic secrets easier to edit     |
|                        | **fixed**      :CLI encryption key works if store path is in a non-default location.    |
|                        | **fixed**      :Client tokens used even if already logged in.     |
|March 2020              | **new feature**: Azure Dynamic Secrets.  DSV can use Azure Service Principals to provide ephermal credentials|
|                        | **new feature**: (API only) Ability to issue X.509 certificates
|                        | **improvement**: Ability to retrieve auth settings by version          |
|                        | **improvement**: Make help commands available even if the CLI config is missing
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
 



