[title]: # (PostgreSQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6430)

# PostgreSQL Dynamic Secrets

## Dynamic Secret Setup

To create Dynamic Secrets for PostgreSQL:

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the PostgreSQL account that will be responsible for creating new PostgreSQL accounts on a given PostgreSQL server. You must mark the secret as an PostgreSQL root secret by including **`type`** with a value of **`postgres`**. All fields in the **`data`** object are required.

    The secret could look like the following:

    ```json
    {
        "path": "db:postgresql:root",
        "attributes": {
        "type": "postgres"
      },
     "data": {
        "database": "postgres",
        "host": "database-2.cjqoldqpaz53.us-east-1.rds.amazonaws.com",
        "password": "myp@ssword",
        "port": 5432,
        "username": "postgres"
      },
     "description": "my postgres base secret"
        }
    }
    ```

1. **Create a new dynamic secret linked to the root secret in the following format.**

```json
{
    "path": "db:postgres:dyn1",
    "attributes": {
        "grantPermissions": {
            "what": "SELECT",
            "where": "*.*"
        },
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "db:postgres:root"
        },
        "pool": "pool1",
        "ttl": 1000
    },
    "data": {},
}
```

 * In the **`linkConfig`**, be sure to specify the path of the root secret as the value of the **`linkedSecret`** key.
* The value of **`linkType`** is always **`dynamic`** for dynamic secrets.
* The **`grantPermissions`** object specifies the permissions assigned by PostgreSQL to the new user account.
* **`ttl`** specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
* The attributes may also include an optional **`userPrefix`** key whose value is a string prepended to all PostgreSQL account usernames created from the dynamic secret.

>**NOTE**: There is no secret data when creating the dynamic secret.

## Sending a PostgreSQL Task to Engine

Read the PostgreSQL dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a PostgreSQL account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
PostgreSQL account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [Postgresql documentation](https://www.postgresql.org/docs/).