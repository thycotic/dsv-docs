[title]: # (Home - Beta)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4900)

## Home Vault

Home provides users with a personal space to store secrets.  No users can access another user's Home vault.  Even the admin does not have access by default, though they can give themselves access for "breakglass" purposes.

As soon as a user is created in DSV, they are given access to thier own Home vault.  No policy has to be created to enable it.




>Note The Home feature is in Beta version currenlty, so some functionality is missing.  Additionally, we my make breaking changes while in Beta.

Commands such as getbyversion, restore, and rollback are not enabled yet.  

If the admin is given access to read users' Home vaults, it can only be done through the API in the Beta version.