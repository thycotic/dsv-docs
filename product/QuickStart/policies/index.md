[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2600)

#Set Policy - Provide Users Access to Secrets

Assuming we have two secrets, each located at:

servers:us-east:server01 *and* servers:us-east:production:server01

And two users:

local@company.com *and* thycoticoneuser@company.com

The admin has to create a policy for the Users to get access to the Secrets.  Here is a sample CLI command:

`thy policy create --path secrets:servers:us-east --actions '<.*>' --desc 'Allow Policy' --subjects 'users:<local@company.com|thy-one:thycoticoneuser@company.com>' --effect 'allow'
`

Where 
*path* starts with **secrets:** followed by the secret path.
>NOTE: That *resources* is not specified separately, but will default to the path and everything below it, so in this case `secrets:servers:us-east:<.*>`

*actions* is a wildcard, so full create, read, update, delete, list, share, and assign access is allowed.

*subjects* are the Users that are getting access to the secrets.  

>Note:  The local user does not need a prefix, but any federated users, in this case Thycotic One, refers to the name of the auth provider.  The default auth-provider name for Thycotic One in DSV is **thy-one**

*effect* is allow

The resulting policy will look like this if you read it using the command `thy policy read secrets:servers:us-east -e yaml`

```bash
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
  - users:<local@company.com|thy-one:thycoticoneuser@company.com>
version: "0"
```

This policy will now enable both Users (local@company.com and thycoticoneuser@company.com) to gain full access to all secrets located at the path `servers:us-east` and below.

If we decided that the *thycoticoneuser@company.com* should no longer have access to the secrets at `servers:us-east:production` we can write another policy to deny that access.  The command would look like this: 

`thy policy create --path secrets:servers:us-east:production --actions '<.*>' --desc 'Deny Policy' --subjects 'users:<thy-one:thycoticoneuser@company.com>' --effect 'deny'`

The resulting policy will look like this if you read it using the command `thy policy read secrets:servers:us-east:production -e yaml`

```bash
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
  - users:<thy-one:thycoticoneuser@company.com>
version: "0"
```

Now local@company.com has access to everything at `servers:us-east` and below, including `servers:us-east:production`.  However, thycoticoneuser@company.com only has access to the secrets at `servers:us-east` and not at `servers:us-east:production`

This is the end of the quick-start guide, but for more on policies see [CLI Reference/Policy](../cli-ref/policy.md) in this documentation.