[title]: # (Authentication: Azure or AWS)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1500)

# Authentication: Azure or AWS

## Editing Your DevOps Secrets Vault Configuration

After reviewing the information in this section, you may want to edit your DSV configuration to make adjustments appropriate to your organization’s infrastructure particulars.

Working on Linux or MacOS, you can use `thy config edit --encoding yaml` to open your configuration in the OS’s default editor (typically **VI** or **nano**). The editor directly updates the configuration in the vault when you save your work.

On Windows, you must use `thy config read -be YAML` to read out the config; save it as a file; edit locally; and use `thy config update --path {path to file} --data \@filename` to upload your work into the vault, entirely overwriting the prior config.

The initial config will look similar to this:

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
  - users:admin
settings: null
tenantName: example
```

In this file, you would modify the `settings` section to include your specific authentication providers, and replace `accountId` (AWS) or `tenantId` (Azure) with your organization’s identifiers.

```yaml
settings:
  authentication:
  - name: aws-dev
    type: aws
    properties:
      accountid: "00000000000"
  - name: azure-prod
    type: azure
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
```

* `name` is the friendly name used for qualifying users and roles in permissions and audits
* `type` is the authentication provider type; valid values as of this release are `aws` and `azure`
* `properties` are configuration settings specific to the authentication provider

---
  
| Property     | Provider      | Description|
| :----------- |:------------- | :-----------|
| accountid    | aws           | AWS account ID where the users or roles will be authorized|
| tenantid     | azure         | Azure tenant ID where the MSIs will be authorized|
  
---
  
> Note: The account identifiers for third-party authentication are a top level setting that allow you or other users to authorize specific security principals within that account. They do not automatically grant access to any user or role within the provider.

Any time you have saved changes to the configuration, you should read the configuration to verify your work. Each property you added will have an auto-generated ID, a unique identifier DSV uses to track changes to the property. All config settings have such IDs.

```bash
thy config read -be yaml
```

Your config should be similar to this:

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
  - users:admin
settings:
  authentication:
  - ID: bfdk8le2bmr1ok3b5u00
    name: aws-dev
    properties:
      accountId: "00000000000"
    type: aws
  - ID: bfdk8le2bmr1ok3b5u0g
    name: azure-prod
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
    type: azure
tenantName: example
```

## Authorizing a Security Principal

After creating a provider account, you should authorize specific roles or users.

### AWS User Example

When you create a user in AWS, remember that the username serves as a friendly name within DSV, and does not have to match the Identity Access Management (IAM) username—but the provider must match the provider name previously configured.

```bash
thy user create --username test-admin --external-id arn:aws:iam::00000000000:user/test-admin --provider aws-dev
```

After creating the user, modify the config to give that user access to the default administrator permission policy. For details on limiting access to specific paths, see the [Policy](../cli-ref/policy.md) section of the [CLI Reference](../cli-ref/index.md).

```bash
thy config edit --encoding yaml
```

Add test-admin as a user subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case, the fully qualified username will be **aws-dev:test-admin**.

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
  - users:<aws-dev:test-admin|admin>
settings:
  authentication:
  - ID: bgno6etchfrc72getij0
    name: aws-dev
    properties:
      accountid: "00000000000"
    type: aws
  - ID: bfdk8le2bmr1ok3b5u0g
    name: azure-prod
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
    type: azure
tenantName: example
```

On a machine with the [AWS CLI](https://aws.amazon.com/cli/) installed and configured with an AWS IAM user, download and initialize the thy CLI.

```bash
thy init --advanced
```

Choose the AWS IAM federated auth option when prompted.

```bash
Please enter auth type:
        (1) Password (default)
        (2) Client Credential
        (3) AWS IAM (federated)
        (4) Azure (federated)
```

When you select `AWS IAM` DSV will prompt for the specific AWS profile to use if you are authenticating using a non-default AWS profile.

```bash
Please enter aws profile for federated aws auth (optional, default:default)
```

Read an existing secret to verify you are able to authenticate and access data.

```bash
thy secret read --path /servers/us-east/server01 -bf data.password
secretp@ssword
```

### AWS Role Example

This example assumes that you:

* have your own thy CLI configured locally with an admin account
* created an IAM role in the AWS Console
* launched an EC2 instance using the IAM role
* [downloaded](https://dsv.thycotic.com/downloads) the thy CLI onto the EC2 instance

Create a corresponding role in DSV with the external-id of the IAM role's ARN.

```bash
thy role create --name test-role --external-id arn:aws:iam::00000000000:role/testlogin --provider aws-dev
```

You should see a result similar to this:

```json
{
  "description": "",
  "externalId": "arn:aws:iam::00000000000:role/testlogin",
  "name": "test-role",
  "provider": "aws-dev"
}
```

Add the role **aws-dev:test-role** to the **Default Admin Policy** in your vault config to grant the new role admin access.

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
  - users:<aws-dev:test-admin|admin>
  - roles:<aws-dev:test-role>
settings:
  authentication:
  - ID: bgno6etchfrc72getij0
    name: aws-dev
    properties:
      accountid: "00000000000"
    type: aws
  - ID: bfdk8le2bmr1ok3b5u0g
    name: azure-prod
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
    type: azure
tenantName: example
```

On the EC2 instance, configure the CLI by running `thy init --advanced` and choosing AWS IAM as the authentication type.

Once configured, you can read an existing secret to verify the EC2 instance is able able to authenticate and access data.

```bash
thy secret read --path /servers/us-east/server01 -bf data.password
secretp@ssword
```

### Azure User Assigned MSI Example

First you will need to configure the user that corresponds to an [Azure User Assigned MSI](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview).

The username is a friendly name within DSV and does not need to match the MSI username. The provider must match the resource id of the MSI in azure.

```bash
thy user create --username test-api --provider azure-prod --external-id /subscriptions/216d58f0-9fa1-49fa-b1f6-81e9f8a12f82/resourcegroups/build/providers/Microsoft.ManagedIdentity/userAssignedIdentities/test-api
```

Modify the config to give that user access to the default administrator permission policy. For details on limiting access to specific paths see the [Policy](../cli-ref/policy.md) section of the [CLI Reference](../cli-ref/index.md).

```bash
thy config edit --encoding yaml
```

Add the user as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name, in this case the fully qualified username will be `azure-prod:test-api`.

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
  - users:<azure-prod:test-api|aws-dev:test-admin|admin>
settings:
  authentication:
  - ID: bgno6etchfrc72getij0
    name: aws-dev
    properties:
      accountid: "00000000000"
    type: aws
  - ID: bfdk8le2bmr1ok3b5u0g
    name: azure-prod
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
    type: azure
tenantName: example
```

On a VM in Azure that is assigned the user MSI as the identity, download and initialize the CLI. Choose the Azure federated authentication option.

```bash
thy init --advanced
```

Read an existing secret to verify you can authenticate and access data.

```bash
thy secret read --path /servers/us-east/server01 -b
```

### Azure Resource Group

If you want to grant access to a set of VM’s in a resource group that use a system assigned MSI rather than a user assigned MSI, you can create a role that corresponds to the resource group’s resource ID.

```bash
thy role create --name identity-rg --provider azure-prod --external-id /subscriptions/216d58f0-9fa1-49fa-b1f6-81e9f8a12f82/resourceGroups/build
```

Modify the config to give that role access to the default administrator permission policy. For details on limiting access to specific paths see the [Policy](../cli-ref/policy.md) section of the [CLI Reference](../cli-ref/index.md).

```bash
thy config edit --encoding yaml
```

Add the user as a subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case the fully qualified role name will be `azure-prod:identity-rg`.

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
  - users:<azure-prod:test-api|aws-dev:test-admin|admin>
  - roles:azure-prod:identity-rg
settings:
  authentication:
  - ID: bgno6etchfrc72getij0
    name: aws-dev
    properties:
      accountid: "00000000000"
    type: aws
  - ID: bfdk8le2bmr1ok3b5u0g
    name: azure-prod
    properties:
      tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed
    type: azure
tenantName: example
```

On a VM in Azure that is part of the resource group and has a system-assigned MSI, run the init command. Choose the Azure auth option when prompted.

```bash
thy init --advanced
```

Read an existing secret to verify you are able to authenticate and access data.

```bash
thy secret read --path /servers/us-east/server01 -b
```

![Article End](../dsv-bug.png)

  
