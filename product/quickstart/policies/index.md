[title]: # (Create Policy)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2600)

# Provide Users Access to Secrets

* With two secrets, each located at:

    servers:us-east:server01 *and* servers:us-east:production:server01

* And two users:

    developer1@company.com *and* developer2@company.com

You can create a policy to allow:

  * both users access to servers:us-east:server01
  * developer1@company.com to **have access** to servers:us-east:production:server01
  * developer2@company.com to be **denied access** to servers:us-east:production:server01

## Create a Group

Optionally, we can put these Users in a Group with two commands. 
* The first command creates the group:

  ```bash
  dsv group create --groupname firstgroup
  ```

* The second command puts the Users in the Group

  ```bash
  dsv group add-members --group-name firstgroup --data '{"memberNames":["developer1@company.com","developer2@company.com"]}'
  ```

## Create Policy to Allow Access

The admin has to create a policy for the Group to get access to the Secrets.  Here is a sample CLI command:

  ```bash
  dsv policy create --path secrets:servers:us-east --actions '<.*>' --desc 'Allow Policy' --subjects groups:firstgroup --effect allow
  ```

* ***path*** starts with **secrets:** followed by the secret path.
    >NOTE: *resources* is not specified separately, but will default to the path and everything below it, so in this case `secrets:servers:us-east:<.*>`

* ***actions*** is a wildcard, so full `create, read, update, delete` are allowed.

* ***subjects*** are the Users that are getting access to the secrets.  

* ***effect*** will either allow or deny access. 

* Use the command **`dsv policy read secrets:servers:us-east -e yaml`** to read the resulting policy:

  ```yaml
    path: secrets:servers:us-east
    permissionDocument:
    - actions:
      - <.*>
      conditions: {}
      description: Allow Policy
      effect: allow
      id: xxxxxxxxxxxxxxxxxxxx
      meta: null
      resources:
      - secrets:servers:us-east:<.*>
      subjects:
      - groups:firstgroup
    version: "0"
    ```

* This policy will now enable both Users (developer1@company.com and developer2@company.com) to gain full access to all secrets located at the path `servers:us-east` and below.

## Create Policy to Deny Access

If we decide that the *developer2@company.com* should no longer have access to the secrets at `servers:us-east:production`, we can write another policy to deny access. The command would look like this:

```bash
dsv policy create --path secrets:servers:us-east:production --actions '<.*>' --desc 'Deny Policy' --subjects 'users:<developer2@company.com>' --effect deny
```

Use the command **`dsv policy read secrets:servers:us-east:production -e yaml`** to view the resulting policy:

```yaml
path: secrets:servers:us-east:production
permissionDocument:
- actions:
  - <.*>
  conditions: {}
  description: Deny Policy
  effect: deny
  id: xxxxxxxxxxxxxxxxxxxx
  meta: null
  resources:
  - secrets:servers:us-east:production:<.*>
  subjects:
  - users:<developer2@company.com>
version: "0"
```

Now developer1@company.com has access to everything at `servers:us-east` and below, including `servers:us-east:production`.  However, developer2@company.com only has access to the secrets at `servers:us-east` and not at `servers:us-east:production`

This is the end of the quick-start guide, but for more on policies see [CLI Reference/Policy](../../cli-ref/policy.md) in this documentation.