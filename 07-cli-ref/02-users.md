[title]: # (Users)
[tags]: # (,)
[priority]: # (7020)

For DSV, the term “users” refers to security principals in the vault that can authenticate locally by a username and password or can authenticate through a federated provider such as AWS or ARN.

## Understanding Qualified Usernames

When a user or role ties to a third-party provider, the name will be the fully qualified name to help distinguish potentially duplicate user or role names across different systems.

The name qualifier format `provider name:local name` means for example that the _test-admin_ user will have the username _aws-dev:test-admin_ while the local user with username _test-admin_ will not have a qualifier, so its username will just be _test-admin_.

## Available Commands

| Command | Action |
| ----- | ----- |
| create | create a user in the vault |
| search | find users by username |
| read | read a user’s details |
| delete | delete a user from the vault |

### Examples

#### Create

The create command takes several `--parameters` that spec foundational aspects of the user record.

| ----- | ----- |
| --username | local username; required; supports local authentication by username and password; need not match that used by a federated authentication provider (if present) |
| --password | password for local authentication by username and password |
| --provider | matches the `name` attribute of the authentication provider in the `settings` section of the config |
| --external-id | identifier recognized by third-party federated authentication providers, such as AWS or ARN |

Create a local user with username _test-admin_ and password _secret-password:

```bash
thy user create --username test-admin --password secret-password
```

Create a user account for login by the AWS IAM _test-admin_ user, with the account tied to an _aws-dev_ account in configuration:

bash
thy user create --username test-admin --external-id arn:aws:iam::00000000000:user/test-admin --provider aws-dev


#### Search

The search command locates users by searching on their usernames. It accepts as a `--query` parameter the username you provide, and searching for records with a matching username.

```bash
thy user search --query test-admin
```

Output:

```

[
  {
    "externalId": "arn:aws:iam::00000000000:user/test-admin",
    "provider": "aws-dev",
    "qualifier": "bgno6etchfrc72getij0",
    "userId": "dd632a7f-419f-400b-9e36-f67603bf934b",
    "userName": "test-admin"
  },
  {
    "externalId": "",
    "provider": "",
    "userId": "8be917b3-9577-4dba-b39f-b531f27c1caa",
    "userName": "test-admin"
  }
]

```

#### Read

Provide a fully qualified username and read the user’s details.

```bash
thy user read --username aws-dev:test-admin
```

Provide a full local username and read the user’s details.

```bash
thy user get --username test-admin
```

#### Delete

Delete will remove records of both local users and users associated with third-party authentication providers. In both cases, you must know the fully qualified username.

Delete a third-party user identified by a fully qualified name.

```bash
thy user delete --username aws-dev:test-admin
```

Delete a local user identified by the full local username.

```bash
thy user delete --username test-admin
```
