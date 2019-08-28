[title]: # (Roles)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1830)

## Roles

With DSV, the term “roles” describes security principals in the vault that tie to third-party providers or client credentials for granting permissions.

### Commands that Act on Roles
  
---
  
| Command | Action |
| ----- | ----- |
| create | create a role in the vault |
| search | find roles by role name |
| read | read a role’s details |
| update | upload a superseding role |
| delete | delete a role from the vault |
  
---
  
### Examples

#### Create

The `create` command takes several `--parameters` that spec key aspects of the role record.
  
---
  
| Parameter | Content |
| ----- | ----- |
| --desc | description of the role |
| --name | name of the role |
| --provider | matches the `name` attribute of the authentication provider in the `settings` section of the config |
| --external-id | identifier recognized by third-party federated authentication providers, such as AWS or ARN |
  
---
  
Create a local role with the name *\_test-role\_*:

```bash
thy role create --name test-role
```

#### Search

The `search` command locates roles by searching on their role names. It accepts as a `--query` parameter the role name you provide, and searches for records with a matching role name.

Search for a role named *\_dev-admin\_*:

```bash
thy role search --query dev-admin
```

Or simply:

```bash
thy role search devadmin
```

You can also specify the maximum number of search results per page (cursor) and a cursor to get the next batch of results.

```bash
thy role search --query us-east/server02 --limit 2 --cursor eyJpZCI6ImZmZjZjODUxTJ2ZXJzaW9uIjo50IiwidHiJ9
```

#### Read

The `read` command retrieves and displays information without changing anything.

Provide a role name and read the role’s details in beautified form:

```bash
thy role read --name test-role -b
```

#### Update

Use `update` to change a role’s data.

>Note that `update` rewrites the **entire** set of role data, even if only a single field has changed. In this way, `update` is highly similar to `create`.

Provide a role name and update the role to replace the description field’s value:

```bash
thy role update --name test-role --desc "a new description"
```

#### Delete

The `delete` command will remove roles.

Provide a role name and delete the role:

```bash
thy role delete --name test-role
```


  

  

______  

![Article End](../dsv-bug.png)

  

