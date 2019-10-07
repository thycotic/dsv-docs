[title]: # (Config)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1850)

# Config

The configuration for DevOps Secrets Vault:

* defines the Default Admin Policy
* contains settings for third-party authentication providers including Thycotic One, AWS or Azure

## Commands that Act on Configurations

| Command | Action                                                                                     |
| ------- | ------------------------------------------------------------------------------------------ |
| read    | view the current configuration                                                             |
| edit    | modify the configuration in an OS-native text editor such as VI or nano (Linux shell only) |
| update  | upload a superseding configuration document                                                |

### Read

To read out the current config, run:

```bash
thy config read -be yaml
```

In this command the `b` flag specifies to beautify the content while the `e` flag specifies YAML (the other option being JSON).

In response, you should see a block of YAML code containing the Default Admin Policy and the authentication configuration for Thycotic One, similar to this.

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
clientId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
```

Federated from Thycotic One, the Thycotic identity provider, the initial user possesses full administrator rights. This enables self-service password reset through Thycotic One.

In keeping with best practices, you should set up a less privileged user policy for routine use. The highly privileged initial Admin account should be used only when a task requires its privileges.

To add users, you first create them in [Thycotic One](https://login.thycotic.com). Then, you enable them within DSV:

```bash
thy user create --username <username> --password <password> --provider
thy-one
```

### Edit

Thycotic recommends against changing the Default Admin Policy other than to add a user.

* For adding and editing policies beyond the Default Admin Policy, see the [Policy](policy.md) article.

Working on Linux or MacOS, use `edit` to open your configuration in the OS’s default editor (typically **VI** or **nano**).

``` bash
thy config edit --encoding YAML
```

The editor directly updates the configuration in the vault when you save your work.

Note: On Windows, you cannot use `edit` on the configuration. Instead, you must:

* use `thy config read -be YAML` to read out the config
* save it as a file
* edit the file locally
* use `thy config update --path {path to file} --data @filename` to upload your work into the vault, entirely overwriting the prior config

### Update

Use `update` to change a config by uploading JSON data.

The value of the `--data` parameter for `update` accepts JSON entered directly at the command line, or the path to a JSON file.

```bash
thy config update --path us-east/server02 --data {\\"something\\":\\"value\\"}
```

or

```bash
thy secret update --path us-east/server02 --data @configfilename.json
```

## Advanced: Add Authentication Providers

Thycotic recommends against changing the Thycotic One provider because it provides for the initial user and any others you add that federate to Thycotic One. However, you can add providers.

### Add AWS as a Provider

To add AWS as an authentication provider, use:

```bash
thy config auth-provider create --name <name> --type <type> --<properties>
```

in which:

* name is the friendly name used in DSV to reference this policy
* type is the authentication provider type; valid values are aws azure and thycoticone
* properties are configuration settings specific to the authentication provider
* the flag for AWS is `--aws-account-id`
* the flag for Azure is `--azure-tenant-id`

### Azure Example

To add Azure as an authentication provider, use:

```bash
thy config auth-provider create --name azure-prod --type azure --azure-tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

```yaml
{
"created": "2019-09-30T23:20:06Z",
"createdBy": "users:thy-one:admin@company.com",
"id": "xxxxxxxxxxxxxxx",
"lastModified": "2019-09-30T23:20:06Z",
"lastModifiedBy": "users:thy-one:admin@company.com",
"name": "azure-prod",
"properties": {
"tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
},
"type": "azure",
"version": "0"
```

Note that the account identifiers for third-party authentication are a top level setting that allow you or other users to authorize specific security principals within that account. They do not automatically grant access to any user or role within the provider.

See the [Authentication: AWS and Azure](../authent-azure-aws/) article for examples of using AWS and Azure for authentication.

![Article End](../dsv-bug.png)
