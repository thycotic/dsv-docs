[title]: # (Oracle Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6420)

# Oracle Dynamic Secrets

## Dynamic Secret Setup

To create Dynamic Secrets for Oracle:

1. **Create a Base Secret**

    In the CLI, create a base secret containing the credentials of the Oracle account that will be responsible for creating new Oracle accounts on a given Oracle server. You must mark the secret as an Oracle root secret by including **`type`** with a value of **`oracle`**. All fields in the **`data`** object are required.

    The secret could look like the following:

    ```json
    {
        "path": "db:oracle:root",
        "data": {
        	"password": "p@ssword!",
    		"username":       "admin",
    		"host":       "database-3.cjqoldqpaz53.us-east-1.rds.amazonaws.com",
	    	"servicename": "ORCL",
    		"port":   1521},
        "description": "oracle root credential",
        "attributes": {
		 "type": "oracle"
    }
    ```

    >**Note**: This host URL is for an Oracle server running in AWS RDS. To use a local server, adjust the host and username.

1. **Create a new dynamic secret linked to the root secret in the following format.**

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

 * In the **`linkConfig`**, be sure to specify the path of the root secret as the value of the **`linkedSecret`** key.
* The value of **`linkType`** is always **`dynamic`** for dynamic secrets.
* The **`grantPermissions`** object specifies the permissions assigned by Oracle to the new user account.
* **`ttl`** specifies the number of seconds for which the new account will exist before the engine automatically deletes it.
* The attributes may also include an optional **`userPrefix`** key whose value is a string prepended to all Oracle account usernames created from the dynamic secret.

>**NOTE**: There is no secret data when creating the dynamic secret.

## Sending an Oracle Task to Engine

Read the Oracle dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a Oracle account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
Oracle account revocation task and delete the account once its TTL expires.

## Third Party Reference

For server configuration details, refer to [Oracle Database Documentation](https://docs.oracle.com/en/database/)