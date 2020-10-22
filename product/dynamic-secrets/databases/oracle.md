[title]: # (Oracle Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6420)

# Oracle Dynamic Secrets
Before creating a Dynamic Secret for Oracle, the DSV Engine must be installed.

## Oracle Engine Requirements:

The Oracle database must have **Oracle Instant Client** installed before running the dsv-engine. DSV only supports the **linux-x64** binary distribution. For other platforms, use docker the distribution.

### Running the Oracle Engine

To run the DSV Engine with install Oracle Instant Client 

1. Install oracle client (https://www.oracle.com/database/technologies/instant-client/downloads.html )
1. Register the engine:
    ```dsv-engine-linux-x64-oracle register --engineName engine01 --secretsvaultcloud.com --tenant acme --userToken <your jwt>```
1. Run the Engine:
    ```Engine run dsv-engine run```

### Docker Setup - PULL from ECR

To run the DSV Engine using Docker

1. Login to AWS ECR ```aws ecr get-login --region us-east-1```
1. Login to Docker.
1. Pull the Engine: ```docker pull 661058921700.dkr.ecr.us-east-1.amazonaws.com/dsv-engine-oracle:latest```
1. Run the Engine:

```CLI
run --env ENGINE_NAME=myengine --env DSV_POOL=pool1 --env DSV_TENANT=mal --env DSV_URL=devbambe.com --env DSV_TOKEN=<jwt> 661058921700.dkr.ecr.us-east-1.amazonaws.com/dsv-engine-oracle-dev:latest
```

## Oracle Dynamic Secret Setup

To create a dynamic secret in Oracle, first create a base secret.

### **Create a Base Secret**

In the CLI, create a base secret containing the credentials of the Oracle account that will be responsible for creating new Oracle accounts on a given Oracle server. You must mark the secret as an Oracle root secret by including **`type`** with a value of **`oracle`**. All fields in the **`data`** object are required.

The secret could look like the following:

```json
    {
        "data": {
        	"password": "your password",
    		    "username": "your username",
    		    "host":     "host",
	    	    "servicename": "servicename",
    		    "port":   1521},
        "description": "oracle root credential",
        "attributes": {
		 "type": "oracle"
    }
}
```

### **Create a new dynamic secret.** 

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
    * `where`: Defines the location within the database for permissions to apply. For `object` privileges, this field should designate the object (ie: ADMIN.EMPLOYEE).
    * `type`: Defines the object permissions within Oracle. Use `system`, `role`, or `object` to grant privileges.

1. **`linkType`** is always **`dynamic`** for dynamic secrets.
1. **`linkedSecret`** should be the path of the root secret.
1. **`pool`**: Designates the Engine pool that DSV will use to generate dynamic secrets.
1. **`ttl`**: Specifies the number of seconds for which the new account will exist before the engine automatically deletes it. **NOTE**: `ttl` must be set **above 900**. 
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