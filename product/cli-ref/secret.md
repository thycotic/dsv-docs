[title]: # (Secret)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1810)

## Secret

Secrets are sensitive data protected in your vault. Many Secrets relate to authentication—such as passwords, SSH keys, and SSL certificates—but Secrets can be anything represented as a file on computer storage media.

When DSV has possession of Secrets outside the vault (that is, the CLI or API has reproduced a Secret anywhere outside the vault), it keeps the Secrets encrypted and locked down in conformance to the specific permissions and policies in the config.

### Commands that Act on Secrets
  
---
  
| Command | Action |
| ----- | ----- |
| bustcache | clear the Secret cache |
| create | create a Secret in the vault |
| search |search for Secrets |
| describe | view Secret metadata only |
| read | view a Secret's data |
| edit | modify a Secret using the OS’s default command-line editor, such as **VI**, **nano**, or **Notepad** |
| update | modify a Secret, with `--data`, `--attributes` and `--desc` flags to modify selected portions only, and a Boolean `--overwrite` flag to control whether the `--data` flag’s content overwrites or merges with extant data object fields  |
| delete | delete a Secret |
| restore | restore a Secret (if within 72 hours of deletion) |
| rollback | for a Secret that has had more than one version, roll back to an earlier version |

   
---
  
### Examples

#### Bustcache

The *bustcache* command clears the local cache, if present.

``` bash
thy secret bustcache
```

Note again here the syntax pattern in which an object of a command precedes the command: *secret* is the object of the command *bustcache*, so that the cache of Secrets will be ‘busted’ (cleared).

#### Create

Secrets data passes into the *create* command as the value of its `--data` parameter. The parameter accepts JSON entered directly at the command line, or the path to a JSON file.

``` bash
thy secret create --path us-east/server02 --data {\"password\":\"Secret\"}
```

If the `--data` parameter’s value will be the path to a JSON file, DSV syntax requires an `@` character immediately preceding the first character of the path. The parameter accepts abolute and relative path strings.

``` bash
thy secret create --path us-east/server03 --data @/home/user/Secret.json
```

``` powershell
thy secret create --path us-east/server02 --data '@/home/user/Secret.json'
```

Using a file in the parent directory:

``` bash
thy secret create --path us-east/server03 --data @../Secret.json
```

``` powershell
thy secret create --path us-east/server02 --data '@../Secret.json'
```

#### Search

You can search for Secrets by path.

``` bash
thy secret search --query us-east/server02
```

Or simply:

``` bash
thy secret search us-east/server02
```

The `--limit` parameter allows you to set the maximum number of search results that will display per page (cursor).

The `--cursor` parameter accepts the element used to get the next page of results.

For a search where there are more results than returned in the first set, the API returns a cursor—a large piece of text. You pass that back to get the next set of results.

For example, if the command `thy secret search -q admin --limit 10` matched 12 Secrets with admin in the name, the CLI would return the first 10 plus a cursor. To obtain the next two results, you would use this command:

```BASH
thy secret search -q admin -limit 10 --cursor AFSDFSD...DKFJLSDJ=
```

Cursors may be lengthy:

```BASH
\thy-win-x64.exe secret search -q resources --limit 10 --cursor eyJpZCI6ImEwOTFjOWIzLWE4MmQtNGRiYy1hYThiLTYxMDY0NDZhZjA3MSIsInBhdGgiOiIiLCJ2ZXJzaW9uIjoidi1jdXJyZW50IiwidHlwZSI6IiIsImxhdGVzdCI6MH0=
```

#### Describe

Use *describe* to show only metadata; you will not see the actual Secret value.

``` bash
thy secret describe --path us-east/server02
```

#### Read

Use *read* to get a Secret’s data. The `-b` flag beautifies the output, while the `-e` flag sets the output format to JSON or YAML and the `-o` flag (as commonly used) redirects the output to a file.

``` bash
thy secret read --path us-east/server02 -b -e yaml
```

Output:

```yaml
attributes: null
data:
  password: Secret
id: 3f15f2a5-f76a-4f43-911c-d8b4f1cc2290
path: us-east:server02
```

The `--filter` or `-f` flag supports filtering to isolate a specific data attribute, for example:

```BASH
thy secret read --path us-east/server02 -f data.password
```

Output:

*Secret*

> TIP: Although the `-o` flag allows redirection of output to files, it does not support directly assigning the output to an environmental variable. However, you can use piping to achieve that outcome.

**Piping** refers to passing to a command a parameter value that is itself a command, or assigning to a variable a value that is a command. In effect, piping means assigning as a value the means to obtain the value, rather than the value itself.

```BASH
export TEST=\$(thy secret read --path us-east/server02)
```

or

```BASH
\$TEST=thy secret read --path us-east/server02
```

Both examples use piping to assign to the variable *TEST* the value contained in the Secret, by making the `secret read` command a parameter within a larger command or statement.

Once stored as the value of *TEST*, the data remain easily accessible:

```BASH
echo \$TEST
```

As a well established computing technique of long standing, piping is not limited to Secrets. You can use piping to store any output—search results, configuration states, and more.

#### Edit

Use *edit* to open the Secret data in the default text editor for bash, such as **vi**, **nano**, or **Notepad**.

* Saving in the editor updates the Secret in the vault, except in the case of Notepad, in which case the update happens when you exit Notepad. Your interim saves are to the working copy.

```BASH
thy secret edit --path us-east/server02
```

#### Update

Use *update* to change a Secret’s data. The command has several flags pertinent to Secrets:

* the `--data` flag allows you to only update the data portion of the Secret
  * the Boolean `--overwrite` flag controls whether the `--data` flag’s content overwrites or merges with extant data object fields
  * the data object accepts as many fields as you choose
* the `--attributes` flag allows you to only update the attributes of the Secret
* the `--desc` flag allows you to only update the description of the Secret

The `--overwrite` flag applies only at the field level; it does not allow you to merge new attributes of a data field into existing attributes of that field, only to merge new data fields into the extant set of data fields.

As with *create*, for the value of the `--data` parameter `update` accepts JSON entered directly at the command line, or the path to a JSON file.

```BASH
thy secret update --path us-east/server02 --data {\\"password\\":\\"Secret2\\"}
```

or

```BASH
thy secret update --path us-east/server02 --data @secret.json
```

#### Delete

To *delete* a Secret simply specify the path.

``` bash
thy secret delete --path us-east/server02
```

When you delete a Secret, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Secret. After 72 hours, the Secret will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command’s `--force` flag.

#### Restore

Up to 72 hours after you delete a Secret, you can restore it:

```bash
thy secret restore --path us-east/server02
```

Do not confuse `restore` with `rollback` because the two have no relation. While `restore` un-deletes a deleted Secret, restoring it to the condition it was in at the time of its deletion, `rollback` does not operate on deleted Secrets. It simply sets a Secret back to an earlier version of itself.

#### Rollback

A Secret that has had more than one version can be rolled back to an earlier version of itself:

```bash
thy secret rollback --path us-east/server02 --version 2
```

If you do not include the `--version` flag, the Secret will roll back to the last version before the present version. By serially issuing the rollback command without a version number, you could step back through the versions one at a time.

Note that the rollback is non-destructive; technically, the command does not roll back so much as retrieve the indicated version and duplicate it as a new version, which becomes the current version.

* If you used the `--version` flag to jump back three versions, you would not lose those three versions; they would remain in place, with the version from three back now being replicated into a new version.

It is important to distinguish between the `rollback` feature, which relates to versions, and the `restore` feature, which relates to the `delete` feature and has nothing to do with versions.

A deleted Secret can be restored up to 72 hours after it has been deleted, after which it cannot be restored. Rollback does not change that in any way, because it cannot operate on a deleted Secret.

If a deleted Secret is restored, Rollback can operate on it just as it would any other Secret.

