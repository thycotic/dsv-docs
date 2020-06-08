[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2600)

#Set Policy - Provide Users Access to Secrets

Assuming we have two secrets, each located at:

servers:us-east:server01 *and* servers:us-east:production:server01

And two users:

local@company.com *and* thycoticoneuser@company.com

The admin has to create a policy for the Users to get access to the Secrets.  Here is a sample CLI command:

```bash
thy policy create --path secrets:servers:us-east --actions '<.*>' --desc 'Developer Policy' --subjects 'users:<local@company.com|thy-one:thycoticoneuser@company.com>' --effect allow
```

Where 
*path* starts with **secrets:** followed by the secret path.
>NOTE: That *resources* is not specified separately, but will default to the path and everything below it, so in this case `secrets:servers:us-east:<.*>`

*actions* is a wildcard, so full create, read, update, delete, list, share, and assign access is allowed.

*subjects* are the Users that are getting access to the secrets.  

>Note:  The local user does not need a prefix, but any federated users, in this case Thycotic One, refers to the name of the auth provider.  The default auth-provider name for Thycotic One in DSV is **thy-one**

*effect* is allow

The resulting policy will look like this:

