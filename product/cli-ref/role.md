[title]: # (Role)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4500)

# Role

With DSV, the term “role” describes a security principal in the vault that ties to third-party providers or client credentials for granting permissions.

## Commands that Act on Roles

![](./images/spacer.png)

| Command | Action |
| ----- | ----- |
| create | create a Role in the vault |
| search | find Roles by Role name |
| read | read a Role’s details |
| update | upload a superseding Role |
| delete | delete a Role from the vault |
| restore | restore a deleted Role to the Vault (if within 72 hours of deletion and not hard deleted) |

![](./images/spacer.png)

## Examples

### Create

The *create* command takes several `--parameters` that spec key aspects of the Role record.

![](./images/spacer.png)

| Parameter | Content |
| ----- | ----- |
| `--desc` | description of the Role |
| `--name` | name of the Role |
| `--provider` | matches the *name* attribute of the authentication provider in the *settings* section of the config |
| `--external-id` | identifier recognized by third-party federated authentication providers, such as AWS or ARN |

![](./images/spacer.png)

Create a local Role with the name *\_test-role\_*:

```BASH
dsv role create --name test-role
```

### Search

The *search* command locates Roles by searching on their Role names. It accepts as a *--query* parameter the Role name you provide, and searches for records with a matching Role name.

Search for a Role named *\_dev-admin\_*:

```BASH
dsv role search --query dev-admin
```

Or simply:

```BASH
dsv role search devadmin
```

You can also specify the maximum number of search results per page (cursor) and a cursor to get the next batch of results.

```BASH
dsv role search --query us-east/server02 --limit 2 --cursor eyJpZCI6ImZmZjZjODUxTJ2ZXJzaW9uIjo50IiwidHiJ9
```

### Read

The *read* command retrieves and displays information without changing anything.

Provide a Role name and read the Role’s details in beautified form:

```BASH
dsv role read --name test-role -b
```

### Update

Use *update* to change a Role’s data.

>Note that *update* rewrites the **entire** set of Role data, even if only a single field has changed.

Provide a Role name and update the Role to replace the description field’s value:

```BASH
dsv role update --name test-role --desc "a new description"
```

### Delete

The *delete* command will remove Roles.

Provide a Role name and delete the Role:

```BASH
dsv role delete --name test-role
```

When you delete a Role, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Role. After 72 hours, the Role will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command’s `--force` flag.

### Restore

Up to 72 hours after you delete a Role (but not if you hard deleted it using the `--force` flag), you can restore it:

```bash
dsv role restore --name test-role
```


![](./images/spacer.png)

![](./images/spacer.png)
