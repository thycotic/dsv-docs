[title]: # (Roles)
[tags]: # (,)
[priority]: # (7030)

With DSV, the term “roles” describes security principals in the vault that tie to third-party providers or client credentials for granting permissions.

## Available Commands

----- | ----- 
create | create a role in the vault
search | find roles by role name
read | read a role’s details
update | upload a superseding role
delete | delete a role from the vault

### Examples

#### Create

The create command takes several `--parameters` that spec key aspects of the role record.

----- | -----
--desc | description of the role
--name | name of the role
--provider | matches the `name` attribute of the authentication provider in the `settings` section of the config
--external-id | identifier recognized by third-party federated authentication providers, such as AWS or ARN


Create a local role with the name _test-role_:

```bash
thy role create --name test-role
```

#### Search

The search command locates roles by searching on their role names. It accepts as a `--query` parameter the role name you provide, and searches for records with a matching role name.

Search for a role named _dev-admin_:

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

Provide a role name and read the role’s details in beautified form.

```bash
thy role read --name test-role -b
```

#### Update

Provide a role name and update the role to replace the description field’s value.

```bash
thy role update --name test-role --desc "a new description"
```

#### Delete

Provide a role name and delete the role.

```bash
thy role delete --name test-role
```

