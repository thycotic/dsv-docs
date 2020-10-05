[title]: # (Client)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4600)

# Client

Client credentials enable applications to authenticate as the Role assigned to the client record.

## Commands that Act on Clients

---

| Command | Action |
| ----- | ----- |
| create | create a User in the vault |
| search | find clients by Role name |
| read | read a client’s details |
| delete | delete a User from the vault |

---

## Examples

### Create

The *create* command accepts as its `--role` parameter a fully qualified Role name, and creates a client credential assigned to that Role.

```BASH
dsv client create --role app-role
```

The output will include a *clientId* and *clientSecret* suitable for use during CLI installation, or within REST calls to authenticate as the Role assigned to the *clientId*.

```BASH
{

"clientId": "a59d37bf-4028-4eb9-9df4-6f1fea7d9298",
"clientSecret": "rV7l8l77DDwTLkdzWkL18UF9blycz3r9yfRhQTYICFc",
"role": "app-role"

}
```

> NOTE: The client Secret is available only when you create the client. If the Secret is lost, delete the client and create a new one.

### Search

The *search* command accepts as its `--query` parameter the name of a Role, and searches for clients having that Role.

```BASH
dsv client search --query dev-role
```

or

```BASH
dsv client search dev-role
```

### Read

The *read* command accepts a client ID as a parameter and returns the details for the given client. As with most commands, remember that you can apply flags to beautify, redirect, or reformat the returned material.

```BASH
dsv client read --client-id a59d37bf-4028-4eb9-9df4-6f1fea7d9298
```

### Delete

The *delete* command accepts a client ID as a parameter and deletes from the vault the indicated client.

```BASH
dsv client delete --client-id a59d37bf-4028-4eb9-9df4-6f1fea7d9298
```

When you delete a Client, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Client. After 72 hours, the Client will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command’s `--force` flag.

### Bootstrapping

There will be times when machines or applications will require access to DSV to get started, but you can't (or don't want) to hardcode the client secret.  In this case, we can create the client ID and get a one-time use URL.  When the URL is accessed, then the corresponding client secret will be created and returned.  The URL will no longer be valid after the initial use, so if the intended machine or application gets an error "url already used" then there should be an alarm to investigate.

 First create the Client ID and URL:

 ```bash
 dsv client create --role <role> --url true --url-ttl <ttl in seconds> 
 ```

 Where "role" is a Role created earlier and is attached to a Policy to provide the proper peermissions.
 "--url" is the flag that tells DSV to create a one-time use URL instead of a Client Secret right now.
 "--url-ttl" is the time to live of the URL in sseconds.  If it is not accessed in that timeframe, then it will become invalid.

The result will look something like this:

```bash
"clientId": "5f1761dd-95ac-479f-a386-f9c379055b04", 
"created": "2020-09-29T13:39:31Z", 
"createdBy": "users:admin@company.com", 
"id": "2f375a20-a670-4843-8b78-502649bc668e", 
"role": "bootstraptest", 
"url": true, 
"urlPath": "https://company.secrestvaultcloud.com/v1/clients/bootstrap/5f1761dd-95ac-479f-a386-f9c379055b04", 
"urlTTL": 3600
```

Then the machine or application can access that urlpath for the Client Secret.  For Example:

```bash
curl https://company.secrestvaultcloud.com/v1/clients/bootstrap/5f1761dd-95ac-479f-a386-f9c379055b04
```

With a result 

```bash
"id":"2f375a20-a670-4843-8b78-502649bc668e",
"clientId":"5f1761dd-95ac-479f-a386-f9c379055b04",
"clientSecret":"r_jqAZz6zs_Toqidv-Paz8wWe9OoP9HyjzRan7t7bc4",
"role":"jenkins",
"url":true,
"accessed":"2020-09-29T13:45:21Z",
"created":"2020-09-29T13:39:31Z",
"createdBy":"users:admin@company.com"
```

If the URL is accessed a second time, then the response will be: `"code":400,"message":"url has already used"`

