[title]: # (CLI Primer)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1600)

# CLI Primer

Like most Command Line Interface Applications, DevOps Secrets Vault combines—

* a base collection of commands,
* the objects on which the commands perform actions,
* the parameters that modify those actions, and
* options flags

—to deliver a task-focused toolset.

## CLI Command Syntax

With few exceptions, CLI commands follow a simple syntax:

  `thy` `object` `command` `flags and parameters`

For example, in `thy role create`, `role` is the object of the command `create`. In English, the command could be written as “the object is a role, and the action to take is to create it.” You will observe this pattern throughout the CLI.

Some parameters and flags apply only to some commands. DSV also includes output modifiers for filtering and formatting responses to commands.

## Commands
  
---
  
| Commmand | Definition |
| ----------- | ------------- |
| auth | authenticate to the vault or display the current access token |
| cli-config | manage DSV settings |
| client | manage client credentials for application vault access |
| config | manage the top level DSV app configuration document shared by all users |
| eval | check the value of a command line flag or variable |
| init | initialize DSV on first run |
| permission | manage permissions for secrets, roles, users, and other entities in the vault |
| role | manage roles |
| secret | create, update, and retrieve secrets from the vault |
| user | manage users |
| whoami | display the currently authenticated user |
  
---
  
## Parameters

Parameters can be:

* strings or numerics
* Boolean
* JSON data
* file path

### Strings

Most commands take strings as parameters, quoted or unquoted. For example, the username needs quote marks but the password does not. Both are valid string parameter values.

`thy user create --username "admin1" --password password2`

If a string value has spaces, it must be wrapped in quotes. For example, when creating a role, the description should be quoted.

`thy role create --name test-role --desc "a test role"`

### Boolean

Some parameters are simple Boolean flags controlling whether or not something applies, for example, whether to beautify the JSON output of a secret read.

`thy secret read --path example/bash-json --beautify`

### JSON Data

In some cases the parameter expects JSON. For example, the `--data` parameter on a `thy secret create` command expects JSON data.

JSON parameter formatting depends on the OS and shell program.

* Linux: wrap the JSON in a single quote (')

* PowerShell: wrap the JSON in a single quote (') and inside the JSON escape each double quote (") with a backslash (\)

* cmd.exe: wrap the JSON in a double quote (") and inside the JSON escape each double quote (") with a backslash (\)

```bash
thy secret create --path example/bash-json --data '{"password":"bash-secret"}'
```

```PowerShell
PS C:\> thy secret create --path example/ps-json --data '{\"password\":\"powershell-secret\"}'
```

```cmd
C:\> thy secret create --path example/cmd-json --data "{\"password\":\"cmd-secret\"}"
```

### File Path

Passing JSON as a parameter remains practical only as long as the JSON remains short. Instead of passing JSON as a parameter, you can pass it as a file, using the \@ prefix to specify the path to the file.

For instance, here the command is to create a secret using a local file named secret.json. The examples show the minor variations among operating systems and shells.

```bash
thy secret create --path example/bash-json --data @secret.json
```

```PowerShell
PS C:\> thy secret create --path example/ps-json --data '@secret.json'
```

```cmd
C:\> thy secret create --path example/cmd-json --data @secret.json
```

For passing a file as data, only Powershell requires the file path and name to be wrapped in quote marks, in this case single-quote marks.

## Output Modifiers

DSV offers global flags that combine with most commands to format or redirect output.

* `--encoding, -e` specify the output format as either JSON or YAML
* `--beautify, -b` beautify JSON or YAML output
* `--filter, -f` filter to output only a specific JSON attribute; this feature uses the [jq library](https://stedolan.github.io/jq/)
* `--out, -o` control the output destination; valid values: `stdout`, `clip`, and `file:[file-name]`, with `stdout` the default

### Encoding and Beautify

`thy secret read --path /servers/us-east/server01 -be yaml`

Outputs:

```yaml
attributes: null
data:
  host: server01
  password: secretp@ssword
  username: administrator
id: c5239a6c-422e-4f57-b3a6-5167656af852
path: servers:us-east:server01
```

### Filter

The filter modifier relies on a lightweight, flexible command line JSON processor, the [jq library](https://stedolan.github.io/jq/).  Visit the JQ GitHub repo to learn more about how to use JQ.

The following code block illustrates:

```JSON
thy secret read --path resources/server01/mysql -b

{
  "attributes": {
    "tag1": "this is a tag"
  },
  "created": "2019-07-17T21:33:35Z",
  "createdBy": "users:ben",
  "data": {
    "foo": ["bar2", "blah"],
    "password": "root-password",
    "username": "blah"
  },
  "id": "59f2ab72-7f51-4f0e-8ffd-35cb94b818fb",
  "lastModified": "2019-07-17T21:36:01Z",
  "lastModifiedBy": "users:ben",
  "path": "resources:server01:mysql",
  "version": "1"
}

thy secret read --path resources/server01/mysql --filter data.password

root-password
```

The command without the filter produced the entire secret, while the command with the filter read out only the password value.

### Out

The `-o` modifier allows output to be redirected to a file.

```cmd
thy secret read --path /servers/us-east/server01 -b -o file:secret.json
\$ nano secret.json
```

Contents of secret.json:

```json
{
  "attributes": null,
  "data": {
    "host": "server01",
    "password": "secretp@ssword",
    "username": "administrator"
  },
  "id": "c5239a6c-422e-4f57-b3a6-5167656af852",
  "path": "servers:us-east:server01"
}
```

Using `-o clip` puts the command output on the OS clipboard.

## Output Piping

Output piping takes advantage of a common coding practice in which the value of a parameter passed to a command is itself a command or set of commands. When the outer command receiving the parameter executes, it evaluates the parameter, which requires it to run the command that was passed as a parameter. The output of that command becomes the parameter value for the outer command, which then continues to execute.

As an example, you can save any DSV CLI output into an environment variable by piping the output from the standard output into an environment variable.

```bash
export MYSECRET=$(thy secret read --path secret1)
```

```powershell
$MYSECRET=thy secret read --path secret1
```

Both of the preceding would create an environment variable named `MYSECRET` that would store the secret data. To view the data you would use:

```bash
echo $MYSECRET
```


  

  

______  

![Article End](../dsv-bug.png)

  
