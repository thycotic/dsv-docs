[title]: # (Using Multiple Profiles)
[tags]: # (,)
[priority]: # (4010)

## Using Multiple Profiles

On initial configuration, DevOps Secrets Vault will have a config with just one profile.

However, DSV supports creating other profiles, potentially with different credentials, and adding them to the config. Once the config has more than one profile, you can set which one DSV will use by default. { *verify: see below* }

## Add a Profile to a Config

DSV syntax gives you two ways to add a profile to the config.

* Run `thy init` and type `add` or `a` at the prompt. Then enter the name of a new profile.

* To do it with one command, run: `thy init --profile [name]` or `thy init --advanced --profile [name]`

## See the Config Contents

If you want to verify the profile has been added, output the updated config contents:

`thy cli-config read`

## Using an Alternate Profile for Most CLI Actions

For a config with more than one profile, you can specify the profile to be used by default using this command:

`thy secret read --path mysecret --profile developer`

So commanded, the CLI will try to auth as the user specified in the `developer` profile and attempt to read the secret as that user.

{ *but does this cause subsequent commands to use the developer profile, that is, does it set the default, as we have said?* }
