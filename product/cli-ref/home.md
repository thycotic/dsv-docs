[title]: # (Home - Beta)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4900)

# Home Vault Beta

Home provides Users with a separate space to store secrets.  No Users can access another User's Home values.  As soon as a User is created in DSV, they are given access to their own Home vault without an explicit policy granting access.

Even the Admin does not have access by default, though they can give themselves access for "breakglass" purposes. If the admin is given access to read users' Home values, it can only be done through the API in the Beta version.

>Note The Home feature is in Beta version currently, so some functionality is missing and we my make breaking changes while in Beta. Commands such as edit, getbyversion, restore, and rollback are not enabled yet.  

>One difference from Secrets to note is that Home has no concept of paths, however, the Home value will list a path like "users:<username>:<secretname>"  DSV will determine which username based on whomever authenticated.  So if joesmith@company.com authenticates, then a creates a Home value, that vaule will be in Joe Smith's Home vault.

Home follows the familiar syntax:
`thy home (command) (flags and parameters)`  with the commands being `create, read, delete, update, describe, search`  The difference between `read` and `describe` is that read shows both data and metadata, while describe only shows metadata.

## Examples

### Create

The `create` command uses the `--data` flag to pass data into the secret.  This flag accepts JSON entered directly into the command line or by a path (absolute or relative) to a JSON file.

Bash examples 

```BASH
thy home create secret1 --data '{"username":"administrator","password":"bash-secret"}'
```

```BASH
thy home create secret2 --data @/home/user/secret.json
```

```BASH
thy home create secret2 --data @../secret.json
```
Powershell examples

```PowerShell
PS C:> thy home create --path secret1 '{\"username\":\"administrator\",\"password\":\"powershell-secret\"}'
```

```PowerShell
thy home create secret2 --data '@/home/user/secret.json'
```

```PowerShell
thy home create secret2 --data '@../secret.json'
```
CMD Examples

```cmd
PS C:> thy home create --path secret1 "{\"username\":\"administrator\",\"password\":\"cmd-secret\"}"
```

```cmd
thy home create secret2 --data @/home/user/secret.json
```

```cmd
thy home create secret2 --data @../secret.json
```
### Read

The `read` command shows both the Secret data and metadata.

```BASH
thy home read secret1 
```

Flags 

`--encoding` or `-e` converts the output to JSON (default) or YAML. 
`--out` or `-o` can send the read response to stdout (default), the clipboard (clip), or a file (file:<filename>)
`--filter` or `-f` filters to a specific KV pair.  So data.password would only output the password value.

This example would send the passowrd value only to the clipboard.

```BASH
thy home read secret2 -o clip -f data.password
```

### Describe

The command `describe` only shows the metadata of a Home value

```BASH
thy home describe secret1 
```

### Search

