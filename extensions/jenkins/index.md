[title]: # (Jenkins)
[tags]: # (,)
[priority]: # (11010)

## Jenkins


### Obtain


### Install


### Use


# Overview
---

The Jenkins plugin allows builds to retrieve secrets from the vault at runtime. 
Secrets can be bound to environment variables in a build step or referenced in a jenkinsfile.


## Installation

* Download the latest version of the Jenkins hpi plugin from https://tools.qabambe.com.
* In Jenkins go to **Manage Jenkins -> Manage Plugins -> Advanced**.
* Click **Browse** and upload the jenkins hpi file.


![Jenkins](assets/img/jenkins-upload.png# border)

## Linking Jenkins to Light Vault

Jenkins needs to be able to query light vault to lookup secrets at build time. In order to do this you must configure a Jenkins credential
to authenticate to your vault.

### Setup Client Credentials
Using the thy CLI create a new client credential linked to a role that has read permissions on secrets Jenkins will need. 

Example commands for bash:

* Create a role - `thy role create --name jenkins --desc "grants access to build secrets" `
* Create a client credential - `thy client create --role jenkins" `
* Save off the clientId and clientSecret returned by `thy client create`. You will use these to grant Jenkins access to the vault.
* Add the jenkins role to a permission policy - `thy config edit -e yaml`

Example permission doc that grants the jenkins role readonly access to secrets under the resources/ path:

```yaml
permissionDocument:
- actions:
  - <.*>
  conditions: {}
  description: Default Admin Policy
  effect: allow
  id: bh516go6kkdc714ojvs0
  meta: null
  resources:
  - <.*>
  subjects:
  - users:<admin>
  - roles:<administrators>
- actions:
  - read
  conditions: {}
  description: Read Policy
  effect: allow
  id: bhm0hnv5725c72kbcv0g
  meta: null
  resources:
  - secrets:resources:<.*>
  subjects:
  - roles:<jenkins>
```


In Jenkins add the newly created client credential

* Under **Credentials** add new credentials.
![Jenkins](assets/img/jenkins-add-credential.png# border)
* Enter in the vault url, your tenant, clientId, and clientSecret from the previously created client credential.
![Jenkins](assets/img/jenkins-add-vault-credential.png# border)
    * You can optionally specify an ID, otherwise jenkins will autogenerate one for you.

### Create a Test Secret
In order to use secrets from the vault in the jenkins build pipelines we will need to create a secret for the
jenkins role to access. Note that in the configuration above, the jenkins role has access to read anything under `resources`. 

We will create a test secret at the path `resources/server01`:


```bash
thy secret create resources/server01 '{"servername":"server01","password":"somepass1"}'
```

Read back the secret to verify the data looks right:

```bash
thy secret resources/server01
```

The secret JSON result should look something like:

```json
{
  "attributes": null,
  "data": {
    "password": "somepass1",
    "servername": "server01"
  },
  "id": "3c679437-9162-4272-8aad-b3cb4ddef4cb",
  "path": "resources:server01"
}
```

During our jenkins builds the plugin will pull the `password` value of "somepass1" from the data property in the secret.

## Freestyle Build
If you don't want to modify an existing build, simply create a new item in Jenkins and select Freestyle project.

To get credentials in a freestyle build:

* Under build environment check the **Thycotic Light Vault Plugin** box
* Choose the credential previously created. This is what will authenticate to the vault to get secrets.
* Add a secret and enter:
    * The path to the secret in the vault.
    * The environment variable you want to bind the secret value to.
    * The secret data field to get the value from. In this case we are getting the value from 
    the "password" field of our previously created secret.
* In build steps you can reference the environment variable as normal. For example the shell script shown will echo out 
the `$MY_PASSWORD` environment variable.


![Jenkins](assets/img/jenkins-build-step.png# border)

* The console output of the build shows the retrieved secret password value of "somepass1" as expected.


![Jenkins](assets/img/jenkins-build-output.png# border)


## Jenkinsfile

In a pipeline you can bind to the plugin to get secrets as environment variables.

The secret referenced is the same one created above, and the field value pulled from `password` on the secret should be "somepass1". 

Set the pipeline script to the following, replacing the key, secret path, and thycoticCredentialId with your values.

```groovy
node {
    // define the env variables
    def secretValues = [
        [$class: 'ThycoticSecretValue', key: 'password', envVar: 'secret']
    ]
    
    // define the path to the secret
    def secrets = [
        [$class: 'ThycoticSecret', path: 'resources/server01', secretValues: secretValues]
    ]

    // set the jenkins credentialid used to connect to the vault
    def configuration = [$class: 'VaultLightConfiguration',
                       thycoticCredentialId: 'vault-jenkins']

    // instantiate the build wrapper to access the populated environment variables
    wrap([$class: 'ThycoticVaultBuildWrapper', configuration: configuration, thycoticVaultSecrets: secrets]) {
        echo "my secret is $secret"
    }
}
```


![Jenkins](assets/img/jenkins-pipeline.png# border)

Run the pipeline, the output will be the password value of the secret from the vault


![Jenkins](assets/img/jenkins-pipeline-output.png# border)

As expected the jenkinsfile outputs the password value from the secret at `resources/server01`.