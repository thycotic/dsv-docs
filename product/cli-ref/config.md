[title]: # (Admin Policy)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4800)

# Policy and Auth Providers

In this section we will

* Define the Default Admin Policy 
* Show settings for third-party authentication providers including Thycotic One, AWS, Azure, or GCP.

## Commands that Act on Policies

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

To read out the current config, which contains the Admin policies

```BASH
thy config read
```

>Note: In this command the `--encoding yaml` flag could be used to provide the output in YAML format.

In response, you should see a block of code containing the Default Admin Policy, similar to this.

```Bash
{
  "created": "2019-09-18T18:38:49Z",
  "createdBy": "system",
  "lastModified": "2020-07-30T23:56:56Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "permissionDocument": [
    {
      "actions": ["<.*>"],
      "conditions": {},
      "description": "Default Admin Permissions",
      "effect": "allow",
      "id": "bm17jee33m1c72u313tg",
      "meta": null,
      "resources": ["<.*>"],
      "subjects": ["users:<thy-one:admin@company.com>"]
    },
    {
      "actions": ["<.*>"],
      "conditions": {},
      "description": "Default Deny Home Permissions",
      "effect": "deny",
      "id": "bsd72rfe1vkc72up3o1g",
      "meta": null,
      "resources": ["home:<.*>"],
      "subjects": ["users:<thy-one:admin@company.com>"]
    }
  ],
  "tenantName": "company",
  "version": "1"
}
```

The initial User possesses full administrator rights and is federated through Thycotic One.  This is indicated by the `thy-one` prefix on the users's email. This enables self-service password reset through Thycotic One.

In keeping with best practices, you should set up a less privileged User policy for routine use. The highly privileged initial Admin account should be used only when a task requires its privileges.

The first section of the Admin policy with the description "Default Admin Permission" is what allows the Admin full rights to everything in DSV.

The second section with the description "Default Deny Home Permissions" denies the Admin permission to access the Home (Beta) feature where users have a place for their own secrets.  If required, the Admin can remove his/her name and then get access to he Home secrets (API only in Beta)

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
    * GCP has two options for federation, GCE metadata and service accounts.
        * For GCE metadata, use *--gcp-projcet-id*
        * Flags are not provided for a service account so a file is required.

>Note: The account identifiers for third-party authentication are a top level setting that allow you or other Users to authorize specific security principals within that account. They do not automatically grant access to any User or Role within the provider.

See the Authentication section for examples of using AWS, Azure, GCP, and Thycotic One for authentication.

To see a list of all Auth-providers:

```BASH
thy config auth-provider search
```
Initially, your tenant will only have a Thycotic One connection

```Bash
    {
      "created": "2019-09-18T18:38:49Z",
      "createdBy": "",
      "id": "bm17jee33m1c72u313u0",
      "lastModified": "2020-05-10T02:25:04Z",
      "lastModifiedBy": "users:admin@company.com",
      "name": "thy-one",
      "properties": {
        "baseUri": "https://thycotic-one-sscdev-dev-eastus-web01.azurewebsites.net",
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
      },
      "type": "thycoticone",
      "version": "1"
    },

![](./images/spacer.png)

![](./images/spacer.png)

