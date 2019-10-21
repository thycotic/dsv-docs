[title]: # (Client)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1840)

## Client

Client credentials enable applications to authenticate as the role assigned to the client record.

### Commands that Act on Clients
  
---
  
| Command | Action |
| ----- | ----- |
| create | create a user in the vault |
| search | find clients by role name |
| read | read a client’s details |
| delete | delete a user from the vault |
  
---
  
### Examples

#### Create

The *create* command accepts as its *--role* parameter a fully qualified role name, and creates a client credential assigned to that role.

```bash
thy client create --role app-role
```

The output will include a *clientId* and *clientSecret* suitable for use during CLI installation, or within REST calls to authenticate as the role assigned to the *clientId*.

```bash
{

"clientId": "a59d37bf-4028-4eb9-9df4-6f1fea7d9298",

"clientSecret": "rV7l8l77DDwTLkdzWkL18UF9blycz3r9yfRhQTYICFc",

"role": "app-role"

}
```

> NOTE: The client Secret is available only when you create the client. If the Secret is lost, delete the client and create a new one.

#### Search

The *search* command accepts as its *--query* parameter the name of a role, and searches for clients having that role.

```bash
thy client search --query dev-role
```

or

```bash
thy client search dev-role
```

#### Read

The *read* command accepts a client ID as a parameter and returns the details for the given client. As with most commands, remember that you can apply flags to beautify, redirect, or reformat the returned material.

```bash
thy client read --client-id a59d37bf-4028-4eb9-9df4-6f1fea7d9298
```

#### Delete

The *delete* command accepts a client ID as a parameter and deletes from the vault the indicated client.

```bash
thy client delete --client-id a59d37bf-4028-4eb9-9df4-6f1fea7d9298
```



  
