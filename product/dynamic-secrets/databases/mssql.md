[title]: # (Microsoft SQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6405)

# Microsoft SQL Dynamic Secrets

Once you have installed the [**DSV Engine**](../../engine/index.md), you can use DSV to create Dynamic Secrets. DSV currently supports **contained** MSSQL databases. 

## Dynamic Secret Setup

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the MSSQL account that will be responsible for creating new accounts on a given server. You must mark the secret as a MSSQL root secret by including **`type`** with a value of **`mssql`**. All fields in the **`data`** object are required.

    *Example Base Secret*:

    ```json
    {
        "attributes": {
        "type": "mssql"
      },
      "data": {
        "database": "TestContainedDB",
        "password": "yourpassword",
        "port": 1433,
        "server": "localhost",
        "username": "yourusername"
      },
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
 "attributes": {
    "grantPermissions": {
      "what": "SELECT",
      "where": "exampletable"
    },
    "linkConfig": {
      "linkType": "dynamic",
      "linkedSecret": "mssql:base2"
    },
    "pool": "pool1",
    "ttl": 900,
    "userPrefix": "test"
  }
}
```

</td>
<td>

1. **`grantPermissions`**: Specifies the permissions assigned by MSSQL to the new user account. 
    * `what`: Defines the database access permissions the user will have in MSSQL. Permissions may include `CONNECT`, `CREATE`, `SELECT`, or other SQL statements.
    * `where`: Defines the location within the database for permissions to apply. 

1. **`linkType`** is always **`dynamic`** for dynamic secrets.
1. **`linkedSecret`** should be the path of the root secret.
1. **`pool`**: Designates the Engine pool that DSV will use to generate dynamic secrets.
1. **`ttl`**: Specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
    > **NOTE**: `ttl` must be set at or **above 900**. 
1. **`userPrefix`** An optional key whose value is a string prepended to all MSSQL account usernames created from the dynamic secret.
1. **`data`**: This field remains blank for dynamic secrets.

</td>
</tr>
</table>


## Sending a MSSQL task to an engine

Read the MSSQL dynamic secret. A randomly chosen engine in the engine pool should receive the task and perform it. The engine attempts to create a MSSQL account and reports back success or failure. On success, the user also receives the new working credentials. As long as there is at least one running engine in a given pool, an engine will receive a MSSQL account revocation task and delete the account once its TTL expires.

## Third Party Reference

For contained server configuration details, refer to [MSSQL Database Documentation](https://docs.microsoft.com/en-us/sql/relational-databases/databases/contained-databases?view=sql-server-ver15)