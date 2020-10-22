[title]: # (Oracle Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6420)

# Oracle Dynamic Secret Setup

To create a dynamic secret in Oracle, first create a base secret.

## **Create a Base Secret**

In the CLI, create a base secret containing the credentials of the Oracle account that will be responsible for creating new Oracle accounts on a given Oracle server. You must mark the secret as an Oracle root secret by including **`type`** with a value of **`oracle`**. All fields in the **`data`** object are required.

The secret could look like the following:

```json
    {
        "path": "db:oracle:root",
        "data": {
        	"password": "p@ssword!",
    		"username": "admin",
    		"host":     "database-3.cjqoldqpaz53.us-east-1.rds.amazonaws.com",
	    	"servicename": "ORCL",
    		"port":   1521},
        "description": "oracle root credential",
        "attributes": {
		 "type": "oracle"
    }
```

>**Note**: This host URL is for an Oracle server running in AWS RDS. To use a local server, adjust the host and username.

## **Create a new dynamic secret.** 

The dynamic secret will be linked to the root secret. Example:

<table>
<tr>
<th> Dynamic Secret
<th> Guide
</tr>
<tr style="vertical-align:top">
<td>

```json
    {
        "path": "db:oracle:dyn1",
        "attributes": {
            "grantPermissions": {
            "what": "CONNECT",
            "where": "none",
            "type": "system"
            },
            "linkConfig": {
                "linkType": "dynamic",
                "linkedSecret": "oracle:base:awsroot"
            },
            "pool": "pool1",
            "ttl": 900
        },
        "data": {},
    }
```

</td>
<td>

1. **`grantPermissions`**: Specifies the permissions assigned by Oracle to the new user account. 
    * `what`: Defines the database access permissions the user will have in Oracle. Permissions may include `CONNECT`, `CREATE`, `SELECT`, or other SQL statements.
    * `where`: Defines the location within the database for permissions to apply.
    * `type`: Defines the object permissions within Oracle. 

1. **`linkType`** is always **`dynamic`** for dynamic secrets.
1. **`linkedSecret`** should be the path of the root secret.
1. **`pool`**: Designates the Engine pool that DSV will use to generate dynamic secrets.
1. **`ttl`**: Specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
1. **`userPrefix`** An optional key whose value is a string prepended to all Oracle account usernames created from the dynamic secret.
1. **`data`**: This field remains blank for dynamic secrets.

</td>
</tr>
</table>

## Sending an Oracle Task to Engine

Read the Oracle dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a Oracle account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
Oracle account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [Oracle Database Documentation](https://docs.oracle.com/en/database/)