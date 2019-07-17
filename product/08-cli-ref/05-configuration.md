[title]: # (Configurations)
[tags]: # (,)
[priority]: # (1850)

# Configurations

Paths record the filesystem locations of executables. The configuration for DevOps Secrets Vault

* defines paths and permissions for paths within the application, and
* contains settings for third-party authentication providers like AWS or ARN, if present

The DSV configuration, or ‘config,’ exists as a YAML or JSON document. Put simply, it is all about permissions.

## Commands that Act on Configurations

| Command | Action |
| ---- | ---- |
| read | view the current configuration |
| edit | modify the configuration in an OS-native text editor such as VI or nano (Linux shell only) |
| update | upload a superseding configuration document |

  

### Examples

#### Read

To read out the current config, run:

```bash
thy config read -be yaml
```

In this command the `b` flag specifies to beautify the content while the `e` flag specifies YAML (the other option being JSON).

In response, you should see a block of YAML code containing a permissions document, beginning like the following, which depicts only the initial lines of what you would see.

```yaml
permissionDocument:
- id: bgn8gjei66jc7148d9i0
  actions:
  - <.*>
  conditions:
    remoteIP:
      type: CIDRCondition
      options:
        cidr: 192.168.0.1/16
  description: Default Admin Policy
  effect: allow
```

For more details about how DVS works with permissions, see the [Permissions](.\06-permissions.md) article.

{ *for the entirety of the content under the following two headings, I am way out on a limb; this is essentially placeholder material consistent in form with the doc for other entities such as `user` or `role`, but no inputs for such were evident, so this is improvised--it requires correction* }

#### Edit

Working on Linux or MacOS, use `edit` to open your configuration in the OS’s default editor (typically **VI** or **nano**).

``` bash
thy config edit --encoding YAML
```

The editor directly updates the configuration in the vault when you save your work.

> Note: On Windows, you cannot use `edit` on the configuration. Instead, you must:
> 
> * use `thy config read -be YAML` to read out the config
> * save it as a file
> * edit the file locally
> * use `thy config update --path {path to file} --data \@filename` to upload your work into the vault, entirely overwriting the prior config.

#### Update

Use `update` to change a config by uploading JSON data.

The value of the `--data` parameter for `update` accepts JSON entered directly at the command line, or the path to a JSON file.

```bash
thy config update --path us-east/server02 --data {\\"something\\":\\"value\\"}
```

or

```bash
thy secret update --path us-east/server02 --data \@configfilename.json
```

