[title]: # (Home - Beta)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4900)

# Home Vault Beta

Home provides Users with a separate space to store Secrets.  No Users can access another User's Home vault.  As soon as a User is created in DSV, they are given access to thier own Home vault.  No Policy has to be created to enable it.

Even the Admin does not have access by default, though they can give themselves access for "breakglass" purposes. 

Home follows the familiar syntax:
`thy home (command) (flags and parameters)`  with the commands being `create, read, delete, update, describe, search`

## Examples

### Create

Secrets in Home can be created direclty 

>Note The Home feature is in Beta version currenlty, so some functionality is missing.  Additionally, we my make breaking changes while in Beta.

Commands such as edit, getbyversion, restore, and rollback are not enabled yet.  

If the admin is given access to read users' Home vaults, it can only be done through the API in the Beta version.