[title]: # (Home - Beta)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4900)

# Home Vault Beta

Home provides Users with a separate space to store secrets.  No Users can access another User's Home values.  As soon as a User is created in DSV, they are given access to their own Home vault without an explicit policy granting access.

The Home value will list a path like "users:<username>:<secretname>"  DSV will determine which username based on whomever authenticated.  So if joesmith@company.com authenticates, then a creates a Home value, that vaule will be in Joe Smith's Home vault.

Even the Admin does not have access by default, though they can give themselves access for "breakglass" purposes. If the admin is given access to read users' Home values, it can only be done through the API in the Beta version.

>Note The Home feature is in Beta version currently, so some functionality is missing and we my make breaking changes while in Beta. Commands such as edit, getbyversion, restore, and rollback are not enabled yet.  



Home follows the familiar syntax:
`thy home (command) (flags and parameters)`  with the commands being `create, read, delete, update, describe, edit, search`  The difference between `read` and `describe` is that read shows both data and metadata, while describe only shows metadata.

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
PS C:> thy home create secret1 "{\"username\":\"administrator\",\"password\":\"cmd-secret\"}"
```

```cmd
thy home create secret2 --data @/home/user/secret.json
```

```cmd
thy home create secret2 --data @../secret.json
```

The `--attributes` flag can be used to add user-defined metadata in the same way that data is added.

The `--desc` flag can be used to add a simple string.  If the string has any spaces, then it should be enclosed in double quotes.

As a Bash example:

```BASH
thy home create secret1 --attributes '{"priority":"high"}'  --desc "Covert Secret"
```

### Update

`update` is similar to `create` but operates on an existing Home value. Only the specified values change unless the `--overwrite' flag is used, in which case all unspecified values are deleted.

If you have this Home value:

``` bash
{
  "attributes": {
    "attr": "add one"
  },
  "created": "2019-09-20T16:12:57Z",
  "createdBy": "users:user@company.com",
  "data": {
    "host": "server01",
    "password": "badpassword"
  },
  "description": "update description",
  "id": "c893b4f8-9425-4fa4-acbf-2806d6f1fa82",
  "lastModified": "2020-01-17T15:43:27Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "users:user@company.com:secret1",
  "version": "12"
}
```
This Bash command will only change the value for *host* in the data section.

``` bash
thy home update secret1 --data '{\"host\":\"unknown\"}'
```

``` bash
{
  "attributes": {
    "attr": "add one"
  },
  "created": "2019-09-20T16:12:57Z",
  "createdBy": "users:user@company.com",
  "data": {
    "host": "unknown",
    "password": "badpassword"
  },
  "description": "update description",
  "id": "c893b4f8-9425-4fa4-acbf-2806d6f1fa82",
  "lastModified": "2020-08-03T17:58:29Z",
  "lastModifiedBy": "users:user@company.com",
  "path": "users:user@company.com:secret1",
  "version": "13"
}
```

The flag `--overwrite`, if added to the above command would wipe-out the description and any other data KV pairs. So this flag requires caution.

``` bash
thy home update secret1 --data '{\"host\":\"unknown\"}' --overwrite
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

This example would send the password value only to the clipboard.

```BASH
thy home read secret2 -o clip -f data.password
```

### Describe

The command `describe` only shows the metadata of a Home value

```BASH
thy home describe secret1 
```

### Search

You can search for Secrets by path or attribute

Some examples

``` bash
thy home search server

thy home search --query server

thy home search --query aws --search-field attributes.type

thy home search --query 900 --search-field attributes.ttl --search-type number

thy home search --query production --search-field attributes.stage --search-comparison equal
```

flags

`--query`, `-q`                Query of secrets to fetch (required)

`--limit`                      Set the maximum number of search results that will display per page (cursor)

`--cursor`                     Accepts the element used to get the next page of results

`--search-comparison`          Specify the operator for advanced field searching, can be 'contains', 'equal', or 'begins_with' Defaults to 'contains' (optional)

`--search-field`               Advanced search on a secret field such as 'attribute.type' or 'description'.  Defaults to 'path'. (optional)

`--search-type`                Specify the value type for advanced field searching, can be 'number' or 'string'. Defaults to 'string' (optional)


For a search where there are more results than returned in the first set, the API returns a cursor—a large piece of text. You pass that back to get the next set of results.

For example, if the command `thy secret search -q admin --limit 10` matched 12 Secrets with admin in the name, the CLI would return the first 10 plus a cursor. To obtain the next two results, you would use this command:

```BASH
thy secret search -q admin --limit 10 --cursor AFSDFSD...DKFJLSDJ=
```

Cursors may be lengthy:

```BASH
thy secret search -q resources --limit 10 --cursor eyJpZCI6ImEwOTFjOWIzLWE4MmQtNGRiYy1hYThiLTYxMDY0NDZhZjA3MSIsInBhdGgiOiIiLCJ2ZXJzaW9uIjoidi1jdXJyZW50IiwidHlwZSI6IiIsImxhdGVzdCI6MH0=
```

### Edit

Use `edit` to open the Secret data in the default text editor for bash, such as **vi**, **nano**, or **Notepad**.

* Saving in the editor updates the Secret in the vault, except in the case of Notepad, in which case the update happens when you save and then exit Notepad. Your interim saves are to the working copy.

```BASH
thy home edit --path us-east/server02
```

### Delete

To `delete` a Hame value, simply specify its name.

``` bash
thy home delete secret1
```

When you delete a Secret, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Secret. After 72 hours, the Secret will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command's `--force` flag.

