[title]: # (Secrets Examples)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2400)

# CLI Secrets Examples

## Create a Secret
### Using a file

Here is an example of JSON that could be made a Secret. The JSON is arbitrary, so you can set any number of fields (key-value pairs).  

```json
{
    "host": "server01",
    "username": "administrator",
    "password": "secretp@ssword"
}
```

To create a Secret, open a text editor and create and save a file (.json) similar to the example above.

Create the Secret and specify the path to its storage location:

>NOTE: Every Secret correlates uniquely with a specific path that describes the location of the Secret. The idea here is no different than the concept of a path to a file on a hard drive. Paths are also the basis for creating policies to determine who (or what) has which rights to those secrets.

Linux:
```BASH
dsv secret create --path servers:us-east:server01 --data @secret.json
```
Powershell:
```PowerShell
dsv secret create --path servers:us-east:server01 --data '@secret.json'
```
CMD:
```cmd
dsv secret create --path servers:us-east:server01 --data @secret.json
```
Outputs:

```json
{
  "attributes": null,
  "created": "2019-01-03T23:11:48Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "server01",
    "password": "secretp@sssword",
    "username": "administrator"
  },
  "description": "",
  "id": "c5239a6c-422e-4f57-b3a6-5167656af852",
  "lastModified": "2019-01-03T23:11:48Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "0"
}
```

Files may also be used to enter attributes `--attributes` or a description `--desc`
### Direct Command

Instead of using a file, the data can be entered as part of the command:

Linux:
```BASH
dsv secret create --path servers:us-east:server01 --data '{"host":"server01","username":"administrator","password":"secretp@sssword"}'
```
Powershell:
```PowerShell
dsv secret create --path servers:us-east:server01 --data '{\"host\":\"server01\",\"username\":\"administrator\",\"password\":\"secretp@sssword\"}'
```
CMD:
```cmd
dsv secret create --path servers:us-east:server01 --data "{\"host\":\"server01\",\"username\":\"administrator\",\"password\":\"secretp@sssword\"}"
```
Outputs:

```json
{
  "attributes": null,
  "created": "2019-01-03T23:11:48Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "server01",
    "password": "secretp@sssword",
    "username": "administrator"
  },
  "description": "",
  "id": "c5239a6c-422e-4f57-b3a6-5167656af852",
  "lastModified": "2019-01-03T23:11:48Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "0"
}
```

## Retrieve a Secret

To retrieve a Secret use the Secret read command and specify the path to the Secret's storage location.

```BASH
dsv secret read --path /servers/us-east/server01
```

Output defaults to JSON:

```json
{
  "attributes": null,
  "created": "2019-11-08T15:46:14Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "server01",
    "password": "secretp@ssword",
    "username": "administrator"
  },
  "description": "",
  "id": "c5239a6c-422e-4f57-b3a6-5167656af852",
  "lastModified": "2020-01-17T15:38:49Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "0"
}
```
If you would like the output to be in YAML:
```BASH
dsv secret read --path /servers/us-east/server01 -e yaml
```
Outputs:
```yaml
attributes: null
created: "2019-11-08T15:46:14Z"
createdBy: users:thy-one:admin@company.com
data:
  host: server01
  password: secretp@ssword
  username: administrator
description: ""
id: c5239a6c-422e-4f57-b3a6-5167656af852
lastModified: "2020-01-17T15:38:49Z"
lastModifiedBy: users:thy-one:admin@company.com
path: servers:us-east:server01
version: "0"
```

## Filter JSON Command Output for Specific Fields

When you need to locate a specific field in a JSON output, use a JSON filter. An example use case is writing scripts that need to obtain a password but lack the capacity to efficiently parse JSON.

```BASH
dsv secret read --path /servers/us-east/server01 -bf data.password
secretp@ssword
```

## Separately Update Attributes, Data, and Description

Using the `--data`, `--attributes`, and `--desc` flags, respectively, you can update a Secret's data, attributes, and description separately. For example:

```BASH
dsv secret update servers/us-east/server01 --data '{"host": "server01", "password": "badpassword","username": "admin"}' --desc 'update description'  --attributes '{"attr": "add one"}'
```
```json
{
  "attributes": {
    "attr": "add one"
  },
  "created": "2019-11-08T15:46:14Z",
  "createdBy": "users:thy-one:admin@company.com",
  "data": {
    "host": "server01",
    "password": "badpassword",
    "username": "admin"
  },
  "description": "update description",
  "id": "4348e941-f945-460d-98e8-2ab659362f51",
  "lastModified": "2020-02-22T20:48:05Z",
  "lastModifiedBy": "users:thy-one:admin@company.com",
  "path": "servers:us-east:server01",
  "version": "1"
}
```

![](./images/spacer.png)

![](./images/spacer.png)