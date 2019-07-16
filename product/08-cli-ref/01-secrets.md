[title]: # (Secrets)
[tags]: # (,)
[priority]: # (1810)

## Secrets

Secrets are sensitive data protected in your vault. Many secrets relate to authentication—such as passwords, SSH keys, and SSL certificates—but secrets can be anything represented as a file on computer storage media.

When DSV has possession of secrets outside the Secret Server Vault (that is, the CLI or API has reproduced a secret anywhere outside the vault), it keeps them encrypted and locked down in conformance to the specific permissions and policies in the config.

### Commands that Act on Secrets

| Command | Action |
| ----- | ----- |
| bustcache | clear the secret cache |
| create | create a secret in the vault |
| search |search for secrets |
| describe | view secret metadata only |
| read | view a secret's data |
| edit | modify a secret, writing only changes to the vault |
| update | modify a secret, writing the entire secret to the vault |
| delete | delete a secret |

  

### Examples

#### Bustcache

The `bustcache` command clears the local cache, if present.

``` bash
thy secret bustcache
```

Note again here the syntax pattern in which an object of a command precedes the command: `secrets` is the object of the command `bustcache`, so that the cache of secrets will be ‘busted’ (cleared).

#### Create

Secrets data passes into the `create` command as the value of its `--data` parameter. The parameter accepts JSON entered directly at the command line, or the path to a JSON file.

``` bash
thy secret create --path us-east/server02 --data {\"password\":\"secret\"}
```

If the `--data` parameter’s value will be the path to a JSON file, DSV syntax requires an `@` character immediately preceding the first character of the path. The parameter accepts abolute and relative path strings.

``` bash
thy secret create --path us-east/server03 --data @/home/user/secret.json
```

``` powershell
thy secret create --path us-east/server02 --data '@/home/user/secret.json'
```

Using a file in the parent directory:

``` bash
thy secret create --path us-east/server03 --data @../secret.json
```

``` powershell
thy secret create --path us-east/server02 --data '@../secret.json'
```

#### Search

You can search for secrets by path.

``` bash
thy secret search --query us-east/server02
```

Or simply:

``` bash
thy secret search us-east/server02
```

The `--limit` parameter allows you to set the maximum number of search results that will display per page (cursor).

The `--cursor` parameter accepts { *what manner of value?* } and sets the element used to get the next page of results.

```bash
thy secret search --query us-east/server02 --limit 2 --cursor eyJpZCI6ImZmZjZjODUxTJ2ZXJzaW9uIjo50IiwidHiJ9
```

#### Describe

Use `describe` to show only metadata; you will not see the actual secret value.

``` bash
thy secret describe --path us-east/server02
```

#### Read

Use `read` to get a secret’s data. As explained in the [CLI Overview](.\05-cli-overview\index.htm) the `-b` flag beautifies the output, while the `-e` flag sets the output format to JSON or YAML and the `-o` flag (as commonly used) redirects the output to a file.

``` bash
thy secret read --path us-east/server02 -b -e yaml
```

Output:

```yaml
attributes: null
data:
  password: secret
id: 3f15f2a5-f76a-4f43-911c-d8b4f1cc2290
path: us-east:server02
```

The `--filter` or `-f` flag supports filtering to isolate a specific data attribute, for example:

```bash
thy secret read --path us-east/server02 -f data.password
```

Output:

`secret`

> TIP: Although the `-o` flag allows redirection of output to files, it does not support directly assigning the output to an environmental variable. However, as explained in the [CLI Overview](.\05-cli-overview\index.htm), you can use piping to achieve that outcome.

**Piping** refers to passing to a command a parameter value that is itself a command, or assigning to a variable a value that is a command. In effect, piping means assigning as a value the means to obtain the value, rather than the value itself.

```bash
export TEST=\$(thy secret read --path us-east/server02)
```

or

```bash
\$TEST=thy secret read --path us-east/server02
```

Both examples use piping to assign to the variable `TEST` the value contained in the secret, by making the `secret read` command a parameter within a larger command or statement.

Once stored as the value of `TEST`, the data remain easily accessible:

```bash
echo \$TEST
```

As a well established computing technique of long standing, piping is not limited to secrets. You can use piping to store any output—search results, configuration states, and more.

#### Edit

Use `edit` to open the secret data in the default text editor for bash, such as **vi** or **nano**.

* Saving in the editor updates the secret in the vault.

``` bash
thy secret edit --path us-east/server02
```

#### Update

Use `update` to change a secret’s data.

>Note that `update` rewrites the **entire** set of secret data, even if only a single field has changed. In this way, `update` is highly similar to `create`.

Like `create`, as the value of the `--data` parameter `update` accepts JSON entered directly at the command line, or the path to a JSON file.

```bash
thy secret update --path us-east/server02 --data {\\"password\\":\\"secret2\\"}
```

or

```bash
thy secret update --path us-east/server02 --data \@secret.json
```

#### Delete

To `delete` a secret simply specify the path.

``` bash
thy secret delete --path us-east/server02
```
