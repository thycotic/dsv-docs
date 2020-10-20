[title]: # (Oracle Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6420)

# Oracle Dynamic Secrets

## Dynamic Secret Setup

In the CLI, create a base secret containing the credentials of the Oracle account that will be responsible for creating new
Oracle accounts on a given Oracle server.

The secret could look like the following:
```
{
    "path": "db:oracle:root",
    "attributes": {
        "type": "oracle"
    },
    "description": "oracle root credentials",
    "data": {
        "host": "database-1.cjqpjhgsaz53.us-east-1.rds.amazonaws.com",
        "password": "P@ssword!",
        "port": 3306,
        "username": "admin"
    }
}
```

The path is arbitrary, as is the description of all secrets. To mark a secret as an Oracle root secret, ensure
its attributes contain a key `type` with a value of `oracle`. All fields in the `data` object are required.

Then create a new dynamic secret linked to the root secret. The secret could look like the following:
```
{
    "path": "db:oracle:dyn1",
    "attributes": {
        "grantPermissions": {
            "what": "SELECT",
            "where": "*.*"
        },
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "db:oracle:root"
        },
        "pool": "pool1",
        "ttl": 1000
    },
    "data": {},
}
```

The path is arbitrary. There is no secret data when creating the dynamic secret. All the necessary information is in the attributes, where all the fields are required.
In the `linkConfig`, be sure to specify the path of the root secret as the value of the `linkedSecret` key. The value of `linkType` is always `dynamic` for dynamic secrets.

The `grantPermissions` object specifies the permissions assigned by Oracle to the new user account. The `ttl` specifies the number
of seconds for which the new account will exist before the engine automatically deletes it.

The attributes may also include an optional `userPrefix` key whose value is a string prepended to all Oracle account usernames
created from the dynamic secret.

## Sending an Oracle Task to Engine

Read the Oracle dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a Oracle account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
Oracle account revocation task and delete the account once its TTL expires.