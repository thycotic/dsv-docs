[title]: # (Authentication: AWS)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5200)

# Authentication: AWS


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
## AWS Authentication Provider 

To add an AWS account to act as an authentication provider:

* `dsv config auth-provider create --name <name> --type aws --aws-account-id <AWS account ID>`

in which:

* name is the friendly name used in DSV to reference this policy
* type is the authentication provider type; in this case, aws.
* the property flag for AWS is `--aws-account-id` then include the account ID


To view the resulting addition to the config file, you would use:

`dsv config auth-provider <name> read -e yaml` where the example name we will use here is aws-dev

The readout would look similar to this:

```yaml
created: "2019-11-12T18:34:49Z"
createdBy: users:thy-one:admin@company.com
id: xxxxxxxxxxxxxxxxxxxx
lastModified: "2020-05-18T03:58:15Z"
lastModifiedBy: users:thy-one:admin@company.com
name: aws-dev
properties:
  accountId: "xxxxxxxxxxxx"
type: aws
version: "0"
```


## AWS User Example

When you create a User in AWS, remember that the username serves as a friendly name within DSV. It does not have to match the Identity Access Management (IAM) username, but the provider must match the provider name previously configured.

```BASH
dsv user create --username test-admin --external-id arn:aws:iam::xxxxxxxxxxx:user/test-admin --provider aws-dev
```

After creating the User, modify the config to give that User access to the default administrator permission policy.

> NOTE: Adding a user to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this AWS user with restricted access.   For details on limiting access through policies, see the [Policy](../../cli-ref/policy.md) section.

```BASH
dsv config edit -e yaml
```

Add *test-admin* as a User subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case, the fully qualified username would be *aws-dev:test-admin*.

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
 - users:<aws-dev:test-admin|admin@company.com>
<snip>
```

Next, on a machine with the [AWS CLI](https://aws.amazon.com/cli/) installed and configured with an AWS IAM user, download the DVS CLI executable appropriate to the OS of the machine, and initialize the CLI:

```BASH
dsv init
```

When prompted for the authorization type, choose *AWS IAM (federated)*.

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

DSV will prompt for the specific AWS profile to use if you are authenticating using a non-default AWS profile.

*Please enter aws profile for federated aws auth (optional, default:default)*

Read an existing Secret to verify you can authenticate to DSV and access data.

```BASH
dsv secret read --path <path to secret>
```

## AWS Role Example

This example assumes that you:

* have your own CLI configured locally with an admin account
* created an IAM Role in the AWS Console
* launched an EC2 instance using the IAM Role
* [downloaded](https://dsv.thycotic.com/downloads) the CLI onto the EC2 instance

Create a corresponding Role in DSV with the external-id of the IAM Role's ARN.

```BASH
dsv role create --name test-role --external-id arn:aws:iam::xxxxxxxxxxx:role/testlogin --provider aws-dev
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

> NOTE: Adding a role to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this AWS role with restricted access.   For details on limiting access through policies, see the [Policy](../../cli-ref/policy.md) section.


Use the command `dsv config edit -e yaml`

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
 - users:<aws-dev:test-admin|admin@company.com>
 - roles:<aws-dev:test-role>
<snip>
```

On the EC2 instance, configure the CLI by running `dsv init` and choosing AWS IAM as the authentication type.

Once configured, ensure you can read an existing Secret to verify the EC2 instance is able able to authenticate and access data.

```BASH
dsv secret read --path <path to secret>
```


![](./images/spacer.png)

![](./images/spacer.png)