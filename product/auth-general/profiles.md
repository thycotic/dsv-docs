[title]: # (Profiles)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5100)

# Profiles

On initial configuration, your DevOps Secrets Vault config will have just one profile with the choices you specified for credentials storage, authentication type, and cache strategy for Secrets.

However, DSV supports creating other profiles, potentially with different credentials, and adding them to the config. Once the config has more than one profile, you can set which one DSV will use by default.

## Add a Profile to a Config

DSV syntax gives you two ways to add a profile to the config.

* Run `dsv init` and type `add` or `a` at the prompt. Then enter the name of a new profile.

* To do it with one command, run `dsv init --profile [name]`.

## See the Config Contents

If you want to verify the profile has been added, output the updated config contents:

```BASH
dsv cli-config read
```

## Using an Alternate Profile for a Specific CLI Action

For a config with more than one profile, the profile used by default for any command will be the first profile created. However, you can override the default by specifying the profile to be used for a  command as a parameter:

```BASH
dsv secret read --path mySecret --profile developer
```

So commanded, the CLI will try to auth as the User specified in the *developer* profile and attempt to read the Secret as that User.

The CLI does not have a command to set the default for all commands moving forward. For that, you should edit the *.thy.yml* file in the home directory to change the profile set as the default.



  
