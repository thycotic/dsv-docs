[title]: # (Using Multiple Profiles)
[tags]: # (,)
[priority]: # (1310)

## Using Multiple Profiles

On initial configuration, your DevOps Secrets Vault config will have just one profile with the choices you specified for credentials storage, authentication type, and cache strategy for secrets.

However, DSV supports creating other profiles, potentially with different credentials, and adding them to the config. Once the config has more than one profile, you can set which one DSV will use by default. { *verify: see below* }

## Add a Profile to a Config

DSV syntax gives you two ways to add a profile to the config.

* Run `thy init` and type `add` or `a` at the prompt. Then enter the name of a new profile.

* To do it with one command, run `thy init --profile [name]` to add a profile with the default settings or (more likely) `thy init --advanced --profile [name]` to add a profile with the settings you choose.

## See the Config Contents

If you want to verify the profile has been added, output the updated config contents:

`thy cli-config read`

## Using an Alternate Profile for Most CLI Actions

For a config with more than one profile, you can specify the profile to be used by default using this command:

`thy secret read --path mysecret --profile developer`

So commanded, the CLI will try to auth as the user specified in the `developer` profile and attempt to read the secret as that user.

{ *this appears to just specify the profile to be used with that one command; what will set the default profile to be used for all commands?* }
