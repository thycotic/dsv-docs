﻿[title]: # (Secret)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4200)

# Secret

Secrets are sensitive data protected in your vault. Many Secrets relate to authentication—such as passwords, SSH keys, and SSL certificates—but Secrets can be anything represented as a file on computer storage media.

When DSV has possession of Secrets outside the vault (that is, the CLI or API has reproduced a Secret anywhere outside the vault), it keeps the Secrets encrypted and locked down in conformance to the specific permissions and policies in the config.

## Commands that Act on Secrets

![](./images/spacer.png)

| Command | Action |
| ----- | ----- |
| bustcache | clear the Secret cache |
| create | create a Secret in the vault |
| search |search for Secrets |
| describe | view Secret metadata only |
| read | view a Secret's data |
| edit | modify a Secret using the OS's default command-line editor, such as **VI**, **nano**, or **Notepad** |
| update | modify a Secret, with `--data`, `--attributes` and `--desc` flags to modify selected portions only, and a Boolean `--overwrite` flag to control whether the `--data` flag's content overwrites or merges with extant data object fields  |
| delete | delete a Secret |
| restore | restore a Secret (if within 72 hours of deletion) |
| rollback | for a Secret that has had more than one version, roll back to an earlier version |

![](./images/spacer.png)

## Examples

### Bustcache

The *bustcache* command clears the local cache, if present.

``` bash
dsv secret bustcache
```

### Create

The `create` command uses the `--data` flag to pass data into the secret.  This flag accepts JSON entered directly into the command line or by a path (absolute or relative) to a JSON file.

Bash examples 

```BASH
dsv secret create --path us-east/server02 --data '{"username":"administrator","password":"bash-secret"}'
```

```BASH
dsv secret create --path us-east/server02 --data @/home/user/secret.json
```

```BASH
dsv secret create --path us-east/server02 --data @../secret.json
```
Powershell examples

```PowerShell
PS C:> dsv secret create --path us-east/server02 --data '{\"username\":\"administrator\",\"password\":\"powershell-secret\"}'
```

```PowerShell
dsv secret create --path us-east/server02 --data '@/home/user/secret.json'
```

```PowerShell
dsv secret create --path us-east/server02 --data '@../secret.json'
```
CMD Examples

```cmd
PS C:> dsv secret create --path us-east/server02 --data "{\"username\":\"administrator\",\"password\":\"cmd-secret\"}"
```

```cmd
dsv home secret --path us-east/server02 --data @/home/user/secret.json
```

```cmd
dsv home secret --path us-east/server02 --data @../secret.json
```

The `--attributes` flag can be used to add user-defined metadata in the same way that data is added.

The `--desc` flag can be used to add a simple string.  If the string has any spaces, then it should be enclosed in double quotes.

As a Bash example:

```BASH
dsv secret create --path us-east/server02 --attributes '{"priority":"high"}'  --desc "Covert Secret" --data '{"username":"administrator","password":"bash-secret"}'
```

### Update

*update* is similar to *create* but operates on an existing secret.  When using *update* for other commands like policy or auth-providers, it is an all or nothing change.  ie, for those if you want to change only one field, you have to update all of them.  However, for Secrets, it is possible to update only one field and not change the others.

If you have this secret:

``` bash
{
  "attributes": {
    "attr": "add one"
  },
  "created": "2019-09-20T16:12:57Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "server01",
    "password": "badpassword"
  },
  "description": "update description",
  "id": "c893b4f8-9425-4fa4-acbf-2806d6f1fa82",
  "lastModified": "2020-01-17T15:43:27Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "12"
}
```
This Bash command will only change the value for *host* in the data section.

``` bash
dsv secret update servers/us-east/server01 --data '{\"host\":\"unknown\"}'
```

``` bash
{
  "attributes": {
    "attr": "add one"
  },
  "created": "2019-09-20T16:12:57Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "unknown",
    "password": "badpassword"
  },
  "description": "update description",
  "id": "c893b4f8-9425-4fa4-acbf-2806d6f1fa82",
  "lastModified": "2020-08-03T17:58:29Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "13"
}
```

The flag `--overwrite`, if added to the above command would wipe-out the description and any other data KV pairs. So this flag requires caution.

``` bash
dsv secret update servers/us-east/server01 --data '{\"host\":\"unknown\"}' --overwrite
```

### Search

You can search for Secrets by path, attribute, or id.

Some examples

``` bash
dsv secret search server

dsv secret search --query server

dsv secret search -q aws:base:secret --search-links

dsv secret search --query aws --search-field attributes.type

dsv secret search --query 900 --search-field attributes.ttl --search-type number

dsv secret search --query production --search-field attributes.stage --search-comparison equal
```

flags

`--query`, `-q`                Query of secrets to fetch (required)

`--limit`                      Set the maximum number of search results that will display per page (cursor)

`--cursor`                     Accepts the element used to get the next page of results

`--search-comparison`          Specify the operator for advanced field searching, can be 'contains', 'equal', or 'begins_with' Defaults to 'contains' (optional)

`--search-field`               Advanced search on a secret field such as 'attribute.type' or 'description'.  Defaults to 'path'. (optional)

`--search-links`               Find secrets that link to the secret path in the query (optional)

`--search-type`                Specify the value type for advanced field searching, can be 'number' or 'string'. Defaults to 'string' (optional)

`--sort`                       Change the sort order using `asc` or `desc` as values. Sort defaults to descending. (optional)


For a search where there are more results than returned in the first set, the API returns a cursor—a large piece of text. You pass that back to get the next set of results.

For example, if the command `dsv secret search -q admin --limit 10` matched 12 Secrets with admin in the name, the CLI would return the first 10 plus a cursor. To obtain the next two results, you would use this command:

```BASH
dsv secret search -q admin --limit 10 --cursor AFSDFSD...DKFJLSDJ=
```

Cursors may be lengthy:

```BASH
dsv secret search -q resources --limit 10 --cursor eyJpZCI6ImEwOTFjOWIzLWE4MmQtNGRiYy1hYThiLTYxMDY0NDZhZjA3MSIsInBhdGgiOiIiLCJ2ZXJzaW9uIjoidi1jdXJyZW50IiwidHlwZSI6IiIsImxhdGVzdCI6MH0=
```

### Describe

Use *describe* to show only metadata; you will not see the actual Secret value.

``` bash
dsv secret describe --path us-east/server02
```

### Read

The `read` command shows both the Secret data and metadata.

```BASH
dsv secret read --path us-east/server02 
```

Flags 

`--encoding` or `-e` converts the output to JSON (default) or YAML. 

`--out` or `-o` can send the read response to stdout (default), the clipboard (clip), or a file (file:<filename>)

`--filter` or `-f` filters to a specific KV pair.  So data.password would only output the password value.

This example would send the password value only to the clipboard.

```BASH
dsv secret read secret2 -o clip -f data.password
```

> TIP: Although the `-o` flag allows redirection of output to files, it does not support directly assigning the output to an environmental variable. However, you can use piping to achieve that outcome.

**Piping** refers to passing to a command a parameter value that is itself a command, or assigning to a variable a value that is a command. In effect, piping means assigning as a value the means to obtain the value, rather than the value itself.

```BASH
export TEST=\$(dsv secret read --path us-east/server02)
```

or

```BASH
\$TEST=dsv secret read --path us-east/server02
```

Both examples use piping to assign to the variable *TEST* the value contained in the Secret, by making the `secret read` command a parameter within a larger command or statement.

Once stored as the value of *TEST*, the data remain easily accessible:

```BASH
echo \$TEST
```

As a well established computing technique of long standing, piping is not limited to Secrets. You can use piping to store any output—search results, configuration states, and more.

### Edit

Use *edit* to open the Secret data in the default text editor for bash, such as **vi**, **nano**, or **Notepad**.

* Saving in the editor updates the Secret in the vault, except in the case of Notepad, in which case the update happens when you exit Notepad. Your interim saves are to the working copy.

```BASH
dsv secret edit --path us-east/server02
```

### Update

Use *update* to change a Secret's data. The command has several flags pertinent to Secrets:

* the `--data` flag allows you to only update the data portion of the Secret
  * the Boolean `--overwrite` flag controls whether the `--data` flag's content overwrites or merges with extant data object fields
  * the data object accepts as many fields as you choose
* the `--attributes` flag allows you to only update the attributes of the Secret
* the `--desc` flag allows you to only update the description of the Secret

The `--overwrite` flag applies only at the field level; it does not allow you to merge new attributes of a data field into existing attributes of that field, only to merge new data fields into the extant set of data fields.

As with *create*, for the value of the `--data` parameter `update` accepts JSON entered directly at the command line, or the path to a JSON file.

```BASH
dsv secret update --path us-east/server02 --data {\\"password\\":\\"Secret2\\"}
```

or

```BASH
dsv secret update --path us-east/server02 --data @secret.json
```

### Delete

To *delete* a Secret simply specify the path.

``` bash
dsv secret delete --path us-east/server02
```

When you delete a Secret, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Secret. After 72 hours, the Secret will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command's `--force` flag.

### Restore

Up to 72 hours after you delete a Secret (but not if you hard deleted it using the `--force` flag), you can restore it:

```bash
dsv secret restore --path us-east/server02
```

Do not confuse `restore` with `rollback` because the two have no relation. While `restore` un-deletes a deleted Secret, restoring it to the condition it was in at the time of its deletion, `rollback` does not operate on deleted Secrets. It simply sets a Secret back to an earlier version of itself.

### Rollback

A Secret that has had more than one version can be rolled back to an earlier version of itself:

```bash
dsv secret rollback --path us-east/server02 --version 2
```

If you do not include the `--version` flag, the Secret will roll back to the last version before the present version. By serially issuing the rollback command without a version number, you could step back through the versions one at a time.

Note that the rollback is non-destructive; technically, the command does not roll back so much as retrieve the indicated version and duplicate it as a new version, which becomes the current version.

* If you used the `--version` flag to jump back three versions, you would not lose those three versions; they would remain in place, with the version from three back now being replicated into a new version.

It is important to distinguish between the `rollback` feature, which relates to versions, and the `restore` feature, which relates to the `delete` feature and has nothing to do with versions.

A deleted Secret can be restored up to 72 hours after it has been deleted (if it was not hard deleted using the `--force` flag), after which it cannot be restored. Rollback does not change that in any way, because it cannot operate on a deleted Secret.

If a deleted Secret is restored, Rollback can operate on it just as it would any other Secret.

