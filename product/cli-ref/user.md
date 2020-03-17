[title]: # (User)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4300)

# User

For DSV, the term "user" refers to a security principal in the vault that can authenticate locally by a username and password or can authenticate through a federated provider such as Amazon Web Services or Amazon Resource Names.

### Understanding Qualified Usernames

When a User or Role ties to a third-party provider, the name will be the fully qualified name to help distinguish potentially duplicate User or Role names across different systems.

The name qualifier format *provider name:local name* means for example that the _test-admin_ User will have the username _aws-dev:test-admin_ while the local User with username _test-admin_ will not have a qualifier, so its username will just be _test-admin_.

## Commands that Act on Users

![](./images/spacer.png)
  
| Command        | Action                         |
| -------------- | ------------------------------ |
| changepassword | change a local User's password |
| create         | create a User in the vault     |
| search         | find Users by username         |
| read           | read a User's details          |
| delete         | delete a User from the vault   |
| restore        | restore a deleted User (if within 72 hours of deletion and not hard deleted) |

![](./images/spacer.png)

## Examples

### Changepassword

The *changepassword* command, effective for local Users only, initiates an elemental password change sequence:

```BASH
thy auth changepassword

Please enter your current password:
*************

Please enter the new password:
*************

Please enter the new password (confirm):
*************
```

With a local User, correct entry for the current password prompt, and valid, matching responses to the first and second prompts for the new password, the response will be a message that the password has been changed.

A Thycotic One Federated User must instead visit Thycotic One to change their password. Attempting to use the *changepassword* command within the CLI will fail.

### Create

The *create* command takes several `--parameters` that spec foundational aspects of the User record.

![](./images/spacer.png)

| Parameter       | Content |
| --------------- | ------- |
| `--username`    | local username; required; supports local authentication by username and password; need not match that used by a federated authentication provider (if present) |
| `--password`    | password for local authentication by username and password |
| `--provider`    | matches the *name* attribute of the authentication provider in the *settings* section of the config |
| `--external-id` | identifier recognized by third-party federated authentication providers, such as AWS or ARN |

![](./images/spacer.png)
  
Create a local User with username *_test-admin_* and password *_secret-password*:

```BASH
thy user create --username test-admin --password secret-password
```

Create a User account for login by the AWS *IAM _test-admin_* User, with the account tied to an *_aws-dev_* account in the configuration:

```BASH
thy user create --username test-admin --external-id arn:aws:iam::00000000000:user/test-admin --provider aws-dev
```

### Search

The *search* command locates Users by searching on their usernames. It accepts as a `--query` parameter the username you provide, and searches for records with a matching username.

```BASH
thy user search --query test-admin
```

Output:

```json

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

### Read

The *read* command retrieves and displays information without changing anything.

Provide a fully qualified username and read the User's details:

```BASH
thy user read --username aws-dev:test-admin
```

Provide a full local username and read the User's details:

```BASH
thy user get --username test-admin
```

### Delete

The *delete* command will remove records of both local Users and Users associated with third-party authentication providers. In both cases, you must provide the fully qualified username.

Delete a third-party User identified by a fully qualified name:

```BASH
thy user delete --username aws-dev:test-admin
```

Delete a local User identified by the full local username:

```BASH
thy user delete --username test-admin
```

When you delete a User, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the User. After 72 hours, the User will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command's `--force` flag.

### Restore

Up to 72 hours after you delete a User (but not if you hard deleted it using the `--force` flag), you can restore it:

```bash
thy user restore --username test-admin
```

![](./images/spacer.png)

![](./images/spacer.png)
