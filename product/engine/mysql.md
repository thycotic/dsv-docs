[title]: # (MySQL Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5600)[title]: # (Engine)


# MySQL Dynamic Secrets for Engine

### Dynamic Secret Setup

First, create a base secret containing the credentials of the MySQL account that will be responsible for creating new
MySQL accounts on a given MySQL server.

The secret could look like the following:
```
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

The path is arbitrary, as is the description, of all secrets. To mark a secret as a MySQL root secret, ensure
its attributes contain a key `type` with a value of `mysql`. All fields in the `data` object are required.

Second, create a new dynamic secret linked to the root secret. The secret could look like the following:
```
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

The path is arbitrary. There is no secret data. All the necessary information is in the attributes, where all the fields are required.
In the `linkConfig`, be sure to specify the path of the root secret as the value of the `linkedSecret` key. The value of 
`linkType` is always `dynamic` for dynamic secrets.

The `grantPermissions` object specifies the permissions assigned by MySQL to the new user account. The `ttl` specifies the number
of seconds for which the new account will exist before the engine automatically deletes it.

The attributes may also include an optional `userPrefix` key whose value is a string prepended to all MySQL account usernames
created from the dynamic secret.

### Engine and MySQL setup

First, ensure you have a pool created in your tenant. Use the [DSV API](https://dsv.thycotic.com/api/index.html) to create a pool specified in the dynamic secret attributes.
Second, create at least one engine in the pool. You can either use the API or run the `dsv-engine` container providing all
the necessary environment variables.

If the MySQL server, for which you plan to create accounts, is not managed by you, then you do not need to do anything.
If, however, you're testing locally, you can start a MySQL server locally with Docker or regular installation.

### Sending a MySQL task to an engine

Read the MySQL dynamic secret. A randomly chosen engine in a pool of engines should receive the task and perform it.
The engine attempts to create a MySQL account and reports back success or failure. On success, the user also receives
the new working credentials. As long as there is at least one running engine in a given pool, some engine will receive a
MySQL account revocation task and delete the account once its TTL expires.