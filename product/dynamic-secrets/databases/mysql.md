[title]: # (MySQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6410)

# MySQL Dynamic Secrets

## Dynamic Secret Setup

To create Dynamic Secrets for MySQL

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the MySQL account that will be responsible for creating new MySQL accounts on a given MySQL server. You must mark the secret as an MySQL root secret by including **`type`** with a value of **`mysql`**. All fields in the **`data`** object are required.

    The secret could look like the following:

    ```json
    {
        "path": "db:mysql:root",
        "attributes": {
            "type": "mysql"
        },
        "description": "mysql root credentials",
        "data": {
            "host": "database-1.cjqpjhgsaz53.us-east-1.rds.amazonaws.com",
            "password": "P@ssword!",
            "port": 3306,
            "username": "admin"
        }
    }
    ```

1. **Create a new dynamic secret.** The dynamic secret will be linked to the root secret. Use the following format:

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
 * In the **`linkConfig`**, be sure to specify the path of the root secret as the value of the **`linkedSecret`** key.
* The value of **`linkType`** is always **`dynamic`** for dynamic secrets.
* The **`grantPermissions`** object specifies the permissions assigned by MySQL to the new user account.
* **`ttl`** specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
* The attributes may also include an optional **`userPrefix`** key whose value is a string prepended to all MySQL account usernames created from the dynamic secret.

>**NOTE**: There is no secret data when creating the dynamic secret.

## Sending a MySQL task to an engine

Read the MySQL dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a MySQL account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
MySQL account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [MySQL Database Documentation](https://dev.mysql.com/doc/)