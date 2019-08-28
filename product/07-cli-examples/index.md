[title]: # (CLI Examples)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1700)

# CLI Examples

Organizations use DSV to secure and protect sensitive information—secrets. Common secrets include passwords, SSH keys, and SSL certificates. While in practice JSON and YAML form the content of most secrets, your vault will accept almost anything represented as a file on computer media.

Every secret correlates uniquely with a specific path that describes the location of the secret within a collection of many other secrets. The idea here is no different than the concept of a path to a file on a hard drive. A path uniquely identifies every secret, and by using paths, you organize your vault and define access permissions.

## Create a Secret

To create a secret, open a text editor and define your secret as JSON.

`$ nano secret.json`

Here is an example of JSON that could be made a secret. The JSON is arbitrary, so you can set any number of fields.

```json
{
    "host": "server01",
    "username": "administrator",
    "password": "secretp@ssword"
}
```

Create the secret and specify the path to its storage location:

```bash
thy secret create --path aws/us-east-1/rds/postgres01 --data @secret.json
```

Outputs:

```json
{
  "attributes": null,
  "created": "2019-01-03T23:11:48Z",
  "description": "",
  "id": "c5239a6c-422e-4f57-b3a6-5167656af852",
  "lastModified": "2019-01-03T23:11:48Z",
  "lastModifiedBy": "users:admin",
  "path": "servers:us-east:server01"
}
```

## Retrieve a Secret

To retrieve a secret use the secret read command and specify the path to the secret’s storage location.

```bash
thy secret read --path /servers/us-east/server01
```

Outputs:

```bash
{"attributes":null,"data":{"host":"server01","password":"secretp@ssword","username":"administrator"},"id":"c5239a6c-422e-4f57-b3a6-5167656af852","path":"servers:us-east:server01"}
```

## Beautify the Output of a Command

To beautify JSON responses to DSV commands, use the `-b` or `--beautify` flag.

```bash
thy secret read --path /servers/us-east/server01 -b
```

Outputs:

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

## Filter JSON Command Output for Specific Fields

When you need to locate a specific field in a JSON output, use a JSON filter. An example use case is writing scripts that need to obtain a password but lack the capacity to efficiently parse JSON.

```bash
thy secret read --path /servers/us-east/server01 -bf data.password
secretp@ssword
```


  

  

______  

![Article End](../dsv-bug.png)

  
