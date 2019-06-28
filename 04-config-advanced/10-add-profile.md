[title]: # (Add a Profile to a Config)
[tags]: # (,)
[priority]: # (4010)
 
On initial configuration, DSV will have a config with just one profile.

However, DSV supports creating other profiles, potentially with different credentials, and adding them to the config. You can set which of the config’s profiles DSV will use by default.

DSV syntax gives you two ways to add a profile to the config.

* Run `thy init` and type `add` or `a` at the prompt. Then enter the name of a new profile.

* Run `thy init --profile [name]`

You can use tthe `--advanced` and `--profile` flags at the same time, for example:

`thy init --advanced --profile [name]`

Assuming `thy init` succeeds, DSV will append the profile to the config. To output the updated config contents, run:

`thy cli-config read`

### Using an Alternate Profile for Most CLI Actions

`thy secret read --path mysecret --profile developer`

With the above snippet, the CLI will try to auth as the user specified in the `developer` profile and attempt to read the secret as that user.


