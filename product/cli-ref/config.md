[title]: # (Config)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4800)

# Config

The configuration for DevOps Secrets Vault:

* defines the Default Admin Policy
* contains settings for third-party authentication providers including Thycotic One, AWS, Azure, or GCP.

## Commands that Act on Configurations

![](./images/spacer.png)

| Command | Action                                                                                     |
| ------- | ------------------------------------------------------------------------------------------ |
| read    | view the current configuration                                                             |
| edit    | modify the configuration in an OS-native text editor such as VI, nano, or Notepad          |
| update  | upload a superseding configuration document                                                |
| delete  | delete a configuration                                                                     |
| restore | restore a deleted configuration (if within 72 hours of deletion and not hard deleted)      |

![](./images/spacer.png)

### Read

To read out the current config, run:

```BASH
thy config read -be yaml
```

In this command the *b* flag specifies to beautify the content while the *e* flag specifies YAML (the other option being JSON).

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

Federated from Thycotic One, the Thycotic identity provider, the initial User possesses full administrator rights. This enables self-service password reset through Thycotic One.

In keeping with best practices, you should set up a less privileged User policy for routine use. The highly privileged initial Admin account should be used only when a task requires its privileges.

To add Users, you first create them in [Thycotic One](https://login.thycotic.com). Then, you enable them within DSV:

```BASH
thy user create --username <username> --password <password> --provider
thy-one
```

### Edit

>NOTE: Thycotic recommends against changing the Default Admin Policy other than to add a User as a back-up admin.  Even then, best practices would be to create a separate policy for specific access for Users.

>NOTE: For adding and editing policies beyond the Default Admin Policy, see the [Policy](policy.md) article.

>NOTE: Thycotic recommends against changing the Thycotic One provider because it provides for the initial User and any others you add that federate to Thycotic One. However, you can add providers.

Use *edit* to open your configuration in the OS’s default editor (typically **VI**, **nano**, or **Notepad**).

``` bash
thy config edit --encoding YAML
```

The editor directly updates the configuration in the vault when you save your work.

### Update

Use *update* to change a config by uploading JSON data.

The value of the `--data` parameter for *update* accepts JSON entered directly at the command line, or the path to a JSON file.

```BASH
thy config update --path us-east/server02 --data {\\"something\\":\\"value\\"}
```

or

```BASH
thy config update --path us-east/server02 --data @configfilename.json
```

### Add an Authentication Provider

The general command is:

```BASH
thy config auth-provider create --name <name> --type <type> --<properties>
```

in which:

* name is the friendly name used in DSV to reference this provider.  It is separate from `type` because it allows multiple auth providers of the same type (for example several AWS accounts).
* type is the authentication provider type; valid values are aws, azure, gcp and thycoticone
* properties are configuration settings specific to the authentication provider
    * AWS flag is *--aws-account-id*
    * Azure flag is *--azure-tenant-id*
    * Thycotic One requires three flags *--baseURI*, *--clientID*, and *--clientSecret* 
    * GCP has two options for federation, GCE metadata and service accounts.  Flags are not provided for these so a file is required.

>Note: The account identifiers for third-party authentication are a top level setting that allow you or other Users to authorize specific security principals within that account. They do not automatically grant access to any User or Role within the provider.

See the Authentication section for examples of using AWS, Azure, GCP, and Thycotic One for authentication.

![](./images/spacer.png)

![](./images/spacer.png)

