[title]: # (Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6000)

# Dynamic Secrets

Dynamic Secrets are automatically generated at the time of request. This differs from the standard Secret store read request where the credentials remain the same until changed by a user. They can be used when you need to provide credentials to a user or resource, like a configuration tool, but the access should expire after a set period of time.

Supported Types:

IaaS Dynamic Secrets
* [AWS](iaas/aws.md)
* [Azure](iaas/azure/index.md)
* [GCP](iaas/gcp.md)

[Database](databases/index.md) Dynamic Secrets
* [MSSQL](databases/mssql.md)
* [MySQL](databases/mysql.md)
* [Oracle](databases/oracle.md)
* [PostgreSQL](databases/postgresql.md)


## Linking

In order for Dynamic Secrets to be generated, they rely on a Base Secret stored in DSV that contains the provider's credentials that are used to automatically generate the ephemeral access keys.
![](./images/spacer.png)

![](./images/DynamicSecretLinking.png)

![](./images/spacer.png)

The linking is done through the `attributes` section in the Secret JSON.  For example the following Secret `temp-api` has no data, but is linked to a different AWS IAM Secret that contains the access and secret key information. The `linkConfig` defines the type of linking and the linked Secret path.

![](./images/spacer.png)


| Attribute                 | Description                                                                                       |
| --------------            | ------------------------------                                                                    |
| linkConfig                | Link type and path to the linked Secret.                                                          |
| linkConfig.linkType       | The only valid value is "dynamic"                                                                 |
| linkConfig.linkedSecret   | Secret path to the base credential                                                                |

```json
{
    "id": "cc619722-6538-4891-b0a6-2c7fa1776a67",
    "path": "dynamic:aws:creds:temp-api",
    "attributes": {
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "base:aws:creds:api-account"
        }
    },
    "description": "",
    "data": {
    }
}
```

## Search for linked Secrets

To get a list of all dynamic secrets linked to a base secret, issue the command `dsv secret search --query <base secret path> --search-links`

![](./images/spacer.png)

![](./images/spacer.png)



