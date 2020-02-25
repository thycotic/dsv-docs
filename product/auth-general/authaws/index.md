[title]: # (Authentication: AWS)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5100)

# Authentication: AWS


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
## AWS Authentication Provider 

To add an AWS account to act as an authentication provider:

* `thy config auth-provider create --name <name> --type aws --aws-account-id <AWS account ID>`

in which:

* name is the friendly name used in DSV to reference this policy
* type is the authentication provider type; in this case, aws.
* the property flag for AWS is `--aws-account-id` then include the account ID


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
- ID: xxxxxxxxxxxxxxxxxxx
name: aws-dev
properties:
accountId: "xxxxxxxxx"
type: aws
tenantName: company
```


## AWS User Example

When you create a User in AWS, remember that the username serves as a friendly name within DSV. It does not have to match the Identity Access Management (IAM) username, but the provider must match the provider name previously configured.

```BASH
thy user create --username test-admin --external-id
arn:aws:iam::xxxxxxxxxxx:user/test-admin --provider aws-dev
```

After creating the User, modify the config to give that User access to the default administrator permission policy.

> NOTE: Adding a user to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this AWS user with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

```BASH
thy config edit --encoding yaml
```

Add *test-admin* as a User subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case, the fully qualified username would be *aws-dev:test-admin*.

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
- users:<aws-dev:test-admin|admin@company.com>
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
name: aws-dev
properties:
accountid: xxxxxxxxxxx
type: aws
tenantName: company
```

Next, on a machine with the [AWS CLI](https://aws.amazon.com/cli/) installed and configured with an AWS IAM user, download the DVS CLI executable appropriate to the OS of the machine, and initialize the CLI:

```BASH
thy init
```

When prompted for the authorization type, choose *AWS IAM (federated)*.

```BASH
Please enter auth type:
       (1) Password (local user)(default)
       (2) Client Credential
       (3) Thycotic One (federated)
       (4) AWS IAM (federated)
       (5) Azure (federated)
```

DSV will prompt for the specific AWS profile to use if you are authenticating using a non-default AWS profile.

*Please enter aws profile for federated aws auth (optional, default:default)*

Read an existing Secret to verify you can authenticate to DSV and access data.

```BASH
thy secret read --path <path to secret>
```

## AWS Role Example

This example assumes that you:

* have your own thy CLI configured locally with an admin account
* created an IAM Role in the AWS Console
* launched an EC2 instance using the IAM Role
* [downloaded](https://dsv.thycotic.com/downloads) the CLI onto the EC2 instance

Create a corresponding Role in DSV with the external-id of the IAM Role’s ARN.

```BASH
thy role create --name test-role --external-id
arn:aws:iam::xxxxxxxxxxx:role/testlogin --provider aws-dev
```

You should see a result similar to this:

```yaml
{
"description": "",
"externalId": "arn:aws:iam::xxxxxxxxxxx:role/testlogin",
"name": "test-role",
"provider": "aws-dev"
}
```

Add the Role *aws-dev:test-role* to the **Default Admin Policy** in your vault config to grant the new Role admin access.

> NOTE: Adding a role to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this AWS role with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

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
- users:<aws-dev:test-admin|admin@company.com>
- roles:<aws-dev:test-role>
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
name: aws-dev
properties:
accountid: xxxxxxxxxxx
type: aws
tenantName: company
```

On the EC2 instance, configure the CLI by running `thy init` and choosing AWS IAM as the authentication type.

Once configured, ensure you can read an existing Secret to verify the EC2 instance is able able to authenticate and access data.

```BASH
thy secret read --path <path to secret>
```


![](./images/spacer.png)

![](./images/spacer.png)