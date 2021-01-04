[title]: # (Authentication: Azure)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5400)

# Authentication: Azure 


Use `dsv config auth-provider search -e yaml` to see all of your current authentication providers.

Initially, the only authentication provider is Thycotic One, similar to this:

```yaml
created: "2019-11-11T20:29:20Z"
createdBy: users:thy-one:admin@company.com
id: xxxxxxxxxxxxxxxxxxxx
lastModified: "2020-05-18T03:58:15Z"
lastModifiedBy: users:thy-one:admin@company.com
name: thy-one
properties:
 baseUri: https://login.thycotic.com/
 clientId: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
 clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
version: "0"
```

## Azure Authentication Provider

To add an Azure account to act as an authentication provider:

* `dsv config auth-provider create --name <name> --type azure --azure-tenant-id <Azure tenant ID>`

where:

* name is the friendly name used in DSV to reference this policy
* type is the authentication provider type; in this case, azure
* the property flag for Azure is `--azure-tenant-id`

To view the resulting addition to the config file, you would use:

`dsv config auth-provider <name> read -e yaml` where the example name we will use here is azure-prod

The readout would look similar to this:

```yaml
created: "2019-11-12T18:34:49Z"
createdBy: users:thy-one:admin@company.com
-id: xxxxxxxxxxxxxxxxxxxxx
lastModified: "2020-05-18T03:58:15Z"
lastModifiedBy: users:thy-one:admin@company.com
name: azure-prod
properties:
 tenantId: xxxxxxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
type: azure
version: "0"
```

## Azure User Assigned MSI Example

First you will need to configure the User that corresponds to an [Azure User Assigned MSI](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview).

The username is a friendly name within DSV. It does not have to match the MSI username, but the provider must match the resource id of the MSI in Azure.

```BASH
dsv user create --username test-api --provider azure-prod --external-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/build/providers/Microsoft.ManagedIdentity/userAssignedIdentities/test-api
```

Modify the config to give that User access to the default administrator permission policy. 

> NOTE: Adding a user to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this Azure user with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

`dsv config edit --encoding yaml`

Add the User as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case the fully qualified username will be *azure-prod:test-api*.

```yaml
<snip>
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
<snip>
```

On a VM in Azure that has the User MSI assigned as the identity, download the DVS CLI executable appropriate to the OS of the VM and initialize the CLI.

```BASH
dsv init
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
       (7) OIDC (federated)
```

Read an existing Secret to verify you can authenticate and access data.

```BASH
dsv secret read --path <path to a secret>
```

## Azure Resource Group

If you want to grant access to a set of VMs in a resource group that use a System assigned MSI rather than a User assigned MSI, you can create a Role that corresponds to the resource group's resource ID.

```BASH
dsv role create --name identity-rg  --external-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/build --provider azure-prod
```

Modify the config to give that Role access to the default administrator permission policy. 

> NOTE: Adding a role to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this Azure role with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

```BASH
dsv config edit --encoding yaml
```

Add the User as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case the fully qualified Role name will be *azure-prod:identity-rg*.

```yaml
<snip>
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
 - roles:<azure-prod:identity-rg>
<snip>
```

On a VM in Azure that is part of the resource group and has a system-assigned MSI, download the DVS CLI executable appropriate to the OS of the VM and initialize the CLI.

```BASH
dsv init
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
dsv secret read --path <path to a secret>
```

![](./images/spacer.png)

![](./images/spacer.png)



