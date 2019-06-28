[title]: # (Authentication • Azure or AWS)
[tags]: # (,)
[priority]: # (9000)

Edit the DSV configuration by running config edit, which opens up a default editor such as nano in most linux shells, on save the configuration will be automatically updated in vault.

!!! note "Secret Permissions" On Windows you must read the config, save it as a file, edit locally, and then run the config update command. The config edit command is only available on Linux and Mac currently.

thy config edit --encoding yaml

The initial config will look something like this:

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:admin   settings: null   tenantName: example

Modify the settings section to include any needed authentication providers. Replace the AWS accountId and Azure tenantId with your corresponding identifiers.

settings:   authentication:   - name: aws-dev   type: aws   properties:   accountid: "00000000000"   - name: azure-prod   type: azure   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed

name - Friendly name used for qualifying users and roles in permissions and audits.

type - The authentication provider type. Valid values are aws and azure.

properties - Provider specific configuration settings.

| Property  | Provider | Description             | |-----------|----------|-----------------------------------------------------------------| | accountid | aws  | The AWS account id where the users or roles will be authorized. | | tenantid  | azure    | The Azure tenant id where the MSI's will be authorized  |

!!! note "Account Identifiers"

The account identifiers for 3rd party authentication are a top level setting that allow you or other users to go on and authorize specific security principals within that account. They do not automatically grant access to any user or role within the provider.

Save the configuration. Read the configuration after saving using the thy config read command. The authentication provider will have an auto-generated id property, which all config settings have to uniquely identify those properties and track changes.

thy config read -be yaml

Your config should look something like this:

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:admin   settings:   authentication:   - ID: bfdk8le2bmr1ok3b5u00   name: aws-dev   properties:   accountId: "00000000000"   type: aws   - ID: bfdk8le2bmr1ok3b5u0g   name: azure-prod   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed   type: azure   tenantName: example

## Authorizing a Security Principal

Once the authentication provider account is created, you will need to authorize specific roles or users.

### AWS User Example

Create the user, the username is a friendly name within DSV and does not need to match the IAM username. The provider must match the provider name previously configured.

thy user create --username test-admin --external-id arn:aws:iam::00000000000:user/test-admin --provider aws-dev

Modify the config to give that user access to the default administrator permission policy. For details on limiting access to specific paths see the permissions section of the guide.

thy config edit --encoding yaml

Add the test-admin as a user subject to the Default Admin Policy. 3rd party accounts must be prefixed with the provider name, in this case the fully qualified username will be aws-dev:test-admin.

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:\<aws-dev:test-admin\|admin\>   settings:   authentication:   - ID: bgno6etchfrc72getij0   name: aws-dev   properties:   accountid: "00000000000"   type: aws   - ID: bfdk8le2bmr1ok3b5u0g   name: azure-prod   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed   type: azure   tenantName: example

On a machine with the [AWS CLI](https://aws.amazon.com/cli/) installed and configured with an AWS IAM user, download and init the thy CLI.

thy init --advanced

Choose the AWS IAM federated auth option when prompted.

Please enter auth type:   (1) Password (default)   (2) Client Credential   (3) AWS IAM (federated)   (4) Azure (federated)

After selecting AWS IAM you will be prompted for the specific AWS profile to use if you are authenticating using a non-default aws profile.

Please enter aws profile for federated aws auth (optional, default:default)

Read an existing secret to verify you are able to authenticate and access data.

thy secret read --path /servers/us-east/server01 -bf data.password   secretp\@ssword

### AWS Role Example

Prerequisites

Have your own thy CLI configured locally with an admin account

Create an IAM role in the AWS Console

Launch an EC2 instance using the IAM role

Download the thy CLI on the EC2 instance from [downloads.lvc.thycotic.com](https://downloads.lvc.thycotic.com)

Create a corresponding role in DSV with the external-id of the IAM role's ARN.

thy role create --name test-role --external-id arn:aws:iam::00000000000:role/testlogin --provider aws-dev

You should see a result similar to:

{   "description": "",   "externalId": "arn:aws:iam::00000000000:role/testlogin",   "name": "test-role",   "provider": "aws-dev"   }

Add the role aws-dev:test-role to the Default Admin Policy in your vault config to grant the new role admin access.

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:\<aws-dev:test-admin\|admin\>   - roles:\<aws-dev:test-role\>   settings:   authentication:   - ID: bgno6etchfrc72getij0   name: aws-dev   properties:   accountid: "00000000000"   type: aws   - ID: bfdk8le2bmr1ok3b5u0g   name: azure-prod   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed   type: azure   tenantName: example

On the EC2 instance, configure the CLI by running thy init --advanced and choosing AWS IAM as the auth type when prompted.

Once configured, you can read an existing secret to verify the EC2 instance is able able to authenticate and access data.

thy secret read --path /servers/us-east/server01 -bf data.password   secretp\@ssword

### Azure User Assigned MSI Example

First you will need to configure the user that corresponds to an [Azure User Assigned MSI](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview).

The username is a friendly name within DSV and does not need to match the MSI username. The provider must match the resource id of the MSI in azure.

thy user create --username test-api --provider azure-prod --external-id /subscriptions/216d58f0-9fa1-49fa-b1f6-81e9f8a12f82/resourcegroups/build/providers/Microsoft.ManagedIdentity/userAssignedIdentities/test-api

Modify the config to give that user access to the default administrator permission policy. For details on limiting access to specific paths see the **Permissions** section of the guide.

thy config edit --encoding yaml

Add the user as a subject to the Default Admin Policy. 3rd party accounts must be prefixed with the provider name, in this case the fully qualified username will be azure-prod:test-api.

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:\<azure-prod:test-api\|aws-dev:test-admin\|admin\>   settings:   authentication:   - ID: bgno6etchfrc72getij0   name: aws-dev   properties:   accountid: "00000000000"   type: aws   - ID: bfdk8le2bmr1ok3b5u0g   name: azure-prod   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed   type: azure   tenantName: example

On a VM in Azure that is assigned the user MSI as the identity download and init the CLI. Choose the Azure federated auth option when prompted.

thy init --advanced

Read an existing secret to verify you are able to authenticate and access data.

thy secret read --path /servers/us-east/server01 -b

### Azure Resource Group Example

If you want to grant access to a set of VMs in a resource group that use a system assigned MSI rather than a user assigned MSI you can create a role that corresponds to the resource group's resource id.

thy role create --name identity-rg --provider azure-prod --external-id /subscriptions/216d58f0-9fa1-49fa-b1f6-81e9f8a12f82/resourceGroups/build

Modify the config to give that role access to the default administrator permission policy. For details on limiting access to specific paths see the permissions section of the guide.

thy config edit --encoding yaml

Add the user as a subject to the Default Admin Policy. 3rd party accounts must be prefixed with the provider name, in this case the fully qualified role name will be azure-prod:identity-rg.

permissionDocument:   - actions:   - \<.\*\>   conditions: {}   description: Default Admin Policy   effect: allow   id: bgn8gjei66jc7148d9i0   meta: null   resources:   - \<.\*\>   subjects:   - users:\<azure-prod:test-api\|aws-dev:test-admin\|admin\>   - roles:azure-prod:identity-rg   settings:   authentication:   - ID: bgno6etchfrc72getij0   name: aws-dev   properties:   accountid: "00000000000"   type: aws   - ID: bfdk8le2bmr1ok3b5u0g   name: azure-prod   properties:   tenantId: e2835781-b2e2-4613-98fd-d0d1eefd25ed   type: azure   tenantName: example

On a VM in Azure that is part of the resource group and has a system assigned MSI run the init command. Choose the Azure auth option when prompted.

thy init

Read an existing secret to verify you are able to authenticate and access data.

thy secret read --path /servers/us-east/server01 -b
