[title]: # (Configure DSV)
[tags]: # (blue prism,DSV)
[priority]: # (2)
# Configure DevOps Secret Vault

Bot Blue Prism must be able to query DevOps Secret Value to look up Secrets at build time. To enable this, you configure a bot credential to authenticate to your vault.

## Setup Client Credentials

Use the DSV CLI to create a new client credential linked to a Role that has read permissions on Secrets BPBot will need.

### Example Commands for Bash

Create a Role  

```BASH
dsv role create --name BPBot --desc "grants access to BP"
```

Create a Client Credential

```BASH
dsv client create --role BPBot
```

Save the *clientId* and *clientSecret* returned by this command. You will use these to grant bot access to the vault.

Add the BPBot Role to a Permission Policy  

```BASH
dsv config edit -e yaml
```

Here is an example permission document granting the BPBot Role read-only access to Secrets under the accounts/path:

```yaml
permissionDocument:
- actions:
  - <.*>
  conditions: {}
  description: Default Admin Policy
  effect: allow
  id: bh516go6kkdc714ojddw
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
  - secrets:accounts:<.*>
  subjects:
  - roles:<BPBot>
```

## Create a Test Secret

To use Secrets from the vault in the Blue Prism build pipelines, we need a Secret for the BPBot Role to access. Note that in the configuration above, the AABot Role has access to read anything under *accounts*.

We will create a test Secret at the path *accounts/blueprism*:

```BASH
dsv secret create accounts/aa '{"username":"bpbot01","password":"testpassword"}'
```

Read back the Secret to verify the data looks right:

```BASH
dsv secret read -be JSON accounts/blueprism
```

The resulting JSON Secret should look similar to:

```json
{
  "attributes": null,
  "data": {
    "password": "testpassword",
    "username": "bpbot"
  },
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "path": "accounts:blueprism"
}
```

During our Blue Prism builds, the extension will pull the *password* value of *testpassword* from the *data* property in the Secret.

More detailed documentation can be studied [DevOps Secret Vault](https://docs.thycotic.com/dsv/1.0.0)
