[title]: # (PostgreSQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6430)

# PostgreSQL Dynamic Secrets

## Dynamic Secret Setup

To create Dynamic Secrets for PostgreSQL:

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the PostgreSQL account that will be responsible for creating new accounts on a given server. You must mark the secret as an PostgreSQL root secret by including **`type`** with a value of **`postgres`**. All fields in the **`data`** object are required.

    *Example Base Secret*:

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

1. **Create a new dynamic secret.** The dynamic secret will be linked to the root secret. Use the following format:

<table>
<tr>
<th> Dynamic Secret
<th> Guide
</tr>
<tr style="vertical-align:top">
<td>

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

</td>
<td>

1. **`grantPermissions`**: Specifies the permissions assigned by PostgreSQL to the new user account. 
    * `what`: Defines the database access permissions the user will have in PostgreSQL. Permissions may include `CONNECT`, `CREATE`, `SELECT`, or other SQL statements.
    * `where`: Defines the location within the database for permissions to apply. 

1. **`linkType`** is always **`dynamic`** for dynamic secrets.
1. **`linkedSecret`** should be the path of the root secret.
1. **`pool`**: Designates the Engine pool that DSV will use to generate dynamic secrets.
1. **`ttl`**: Specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
    > **NOTE**: `ttl` must be set **above 900**. 
1. **`userPrefix`** An optional key whose value is a string prepended to all PostgreSQL account usernames created from the dynamic secret.
1. **`data`**: This field remains blank for dynamic secrets.

</td>
</tr>
</table>

## Sending a PostgreSQL Task to Engine

Read the PostgreSQL dynamic secret. A randomly chosen engine in the engine pool should receive the task and perform it. The engine attempts to create a PostgreSQL account and reports back success or failure. On success, the user also receives the new working credentials. As long as there is at least one running engine in a given pool, an engine will receive a PostgreSQL account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [Postgresql documentation](https://www.postgresql.org/docs/).