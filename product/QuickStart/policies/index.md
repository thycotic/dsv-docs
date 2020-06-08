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
thy policy create --path secrets:servers:us-east-1 --actions '<.*>' --desc 'Developer Policy' --subjects 'users:<local@company.com|thy-one:thycoticoneuser@company.com>' --effect allow
```

Where 
