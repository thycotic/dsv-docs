[title]: # (Authentication: Azure)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5400)

# Authentication: Azure 


Use `thy config read --encoding yaml` to see your current configuration.

The initial config will look similar to this:

```yaml
permissionDocument:
- actions:
- <.*>
conditions: {}
description: Default Admin Policy
effect: allow
id: xxxxxxxxxxxxxxxxxxxx
meta: null
resources:
- <.*>
subjects:
- users:<thy-one:admin@company.com>
settings:
authentication:
- ID: xxxxxxxxxxxxxxxxxxxx
name: thy-one
properties:
baseUri: https://login.thycotic.com/
clientId: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
tenantName: company
```

## Azure Authentication Provider

To add an Azure account to act as an authentication provider:

* `thy config auth-provider create --name <name> --type azure --azure-tenant-id <Azure tenant ID>`

where:

* name is the friendly name used in DSV to reference this policy
* type is the authentication provider type; in this case, azure
* the property flag for Azure is `--azure-tenant-id`

To view the resulting addition to the config file, you would use:

```BASH
thy config read -be yaml
```

The readout would look similar to this:

```yaml
permissionDocument:
- actions:
- <.*>
conditions: {}
description: Default Admin Policy
effect: allow
id: xxxxxxxxxxxxxxxxxxxxx
meta: null
resources:
- <.*>
subjects:
- users:<thy-one:admin@company.com>
settings:
authentication:
- ID: xxxxxxxxxxxxxxxxxx
name: thy-one
properties:
baseUri: https://login.thycotic.com/
clientId: xxxxxxxx-xxxxxxxxx-xxxx-xxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
- ID: xxxxxxxxxxxxxxxxxxxxx
name: azure-prod
properties:
tenantId: xxxxxxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
type: azure
tenantName: company
```

## Azure User Assigned MSI Example

First you will need to configure the User that corresponds to an [Azure User Assigned MSI](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview).

The username is a friendly name within DSV. It does not have to match the MSI username, but the provider must match the resource id of the MSI in Azure.

```BASH
thy user create --username test-api --provider azure-prod --external-id
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/build/providers/Microsoft.ManagedIdentity/userAssignedIdentities/test-api
```

Modify the config to give that User access to the default administrator permission policy. 

> NOTE: Adding a user to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this Azure user with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

`thy config edit --encoding yaml`

Add the User as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case the fully qualified username will be *azure-prod:test-api*.

```yaml
permissionDocument:
- actions:
- <.*>
conditions: {}
description: Default Admin Policy
effect: allow
id: xxxxxxxxxxxxxxxxxxxx
meta: null
resources:
- <.*>
subjects:
- users:<azure-prod:test-api|admin@company.com>
settings:
authentication:
- ID: xxxxxxxxxxxxxxxxxx
name: thy-one
properties:
baseUri: https://login.thycotic.com/
clientId: xxxxxxxx-xxxxxxxxx-xxxx-xxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
- ID: xxxxxxxxxxxxxxxxxxxx
name: azure-prod
properties:
tenantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
type: azure
tenantName: company
```

On a VM in Azure that has the User MSI assigned as the identity, download the DVS CLI executable appropriate to the OS of the VM and initialize the CLI.

```BASH
thy init
```

When prompted for the authorization type, choose the *Azure (federated)* authentication option.

```BASH
Please enter auth type:
       (1) Password (local user)(default)
       (2) Client Credential
       (3) Thycotic One (federated)
       (4) AWS IAM (federated)
       (5) Azure (federated)
       (6) GCP (federated)
```

Read an existing Secret to verify you can authenticate and access data.

```BASH
thy secret read --path <path to a secret>
```

## Azure Resource Group

If you want to grant access to a set of VMs in a resource group that use a System assigned MSI rather than a User assigned MSI, you can create a Role that corresponds to the resource group's resource ID.

```BASH
thy role create --name identity-rg  --external-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/build --provider azure-prod
```

Modify the config to give that Role access to the default administrator permission policy. 

> NOTE: Adding a role to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this Azure role with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

```BASH
thy config edit --encoding yaml
```

Add the User as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case the fully qualified Role name will be *azure-prod:identity-rg*.

```yaml
permissionDocument:
- actions:
- <.*>
conditions: {}
description: Default Admin Policy
effect: allow
id: bgn8gjei66jc7148d9i0
meta: null
resources:
- <.*>
subjects:
- users:<azure-prod:test-api|admin@company.com>
- roles:azure-prod:identity-rg
settings:
authentication:
- ID: xxxxxxxxxxxxxxxxxx
name: thy-one
properties:
baseUri: https://login.thycotic.com/
clientId: xxxxxxxx-xxxxxxxxx-xxxx-xxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
- ID: xxxxxxxxxxxxxxxxxxxx
name: azure-prod
properties:
tenantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
type: azure
tenantName: company
```

On a VM in Azure that is part of the resource group and has a system-assigned MSI, download the DVS CLI executable appropriate to the OS of the VM and initialize the CLI.

```BASH
thy init
```

When prompted for the authorization type, choose the *Azure (federated)* option.

```BASH
Please enter auth type:
       (1) Password (local user)(default)
       (2) Client Credential
       (3) Thycotic One (federated)
       (4) AWS IAM (federated)
       (5) Azure (federated)
       (6) GCP (federated)
```

Read an existing Secret to verify you are able to authenticate and access data.

```BASH
thy secret read --path <path to a secret>
```

![](./images/spacer.png)

![](./images/spacer.png)



