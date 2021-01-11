[title]: # (MySQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6410)

# MySQL Dynamic Secrets

Once you have installed the [**DSV Engine**](../../engine/index.md), you can use DSV to create Dynamic Secrets.

## Dynamic Secret Setup

To create Dynamic Secrets for MySQL

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the MySQL account that will be responsible for creating new accounts on a given server. You must mark the secret as a MySQL root secret by including **`type`** with a value of **`mysql`**. All fields in the **`data`** object are required.

    >**Note**: Port is an integer and does not require quotations.

    *Example Base Secret*:

    ```json
    {
        "path": "db:mysql:root",
        "attributes": {
            "type": "mysql"
        },
        "description": "mysql root credentials",
        "data": {
            "host": "yourhost.name",
            "password": "yourpassword",
            "port": 3306,
            "username": "yourname"
        }
    }
    ```

1. **Create a new dynamic secret.** The dynamic secret will be linked to the root secret. Use the following format:

**Dynamic Secret Example**

```json
{
    "path": "db:mysql:dyn1",
    "attributes": {
        "grantPermissions": {
            "what": "SELECT",
            "where": "*.*"
        },
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "db:mysql:root"
        },
        "pool": "pool1",
        "ttl": 1000
    },
    "data": {},
}
```

**Dynamic Secret Guide**

1. **`grantPermissions`**: Specifies the permissions assigned by MySQL to the new user account. 
    * `what`: Defines the database access permissions the user will have in MySQL. Permissions may include `CONNECT`, `CREATE`, `SELECT`, or other SQL statements.
    * `where`: Defines the location within the database for permissions to apply. 

1. **`linkType`** is always **`dynamic`** for dynamic secrets.
1. **`linkedSecret`** should be the path of the root secret.
1. **`pool`**: Designates the Engine pool that DSV will use to generate dynamic secrets.
1. **`ttl`**: Specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
    > **NOTE**: `ttl` must be set at or **above 900**. 
1. **`userPrefix`** An optional key whose value is a string prepended to all MySQL account usernames created from the dynamic secret.
1. **`data`**: This field remains blank for dynamic secrets.

</td>
</tr>
</table>


## Sending a MySQL task to an engine

Read the MySQL dynamic secret. A randomly chosen engine in the engine pool should receive the task and perform it. The engine attempts to create a MySQL account and reports back success or failure. On success, the user also receives the new working credentials. As long as there is at least one running engine in a given pool, an engine will receive a MySQL account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [MySQL Database Documentation](https://dev.mysql.com/doc/)