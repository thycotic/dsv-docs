[title]: # (Configure DSV • Basics)
[tags]: # (,)
[priority]: # (3000)

# Configure DSV • Basics

Knowing how DSV stores its settings makes DSV configuration work straightforward. 

## Configs and Profiles

The combination of all the configuration settings for an installation of DSV exists as a YAML or JSON file. For the purposes of DSV, this file is a **config** and the distinct set of configuration settings it records is a **profile**.

After installation and initial setup, whether basic or advanced, the config contains only the one profile created during initial setup, but you can add other profiles to the config and set which profile DSV will use by default.

Use the instructions here to obtain the default DSV configuration. For a custom configuration, or to add a new profile to an existing config, use instead the instructions in [Configure DSV • Advanced](..\04-configure-advanced\index.htm).

## Configuring with Default Settings

To configure DSV with default settings, use the **thy init** command.

```bash
thy init
Please enter tenant name: acme
```

Specify the tenant name Thycotic provided when setting up your organization’s account. Do not specify the full domain—for example, **acme**, not **acme.secretsvaultcloud.com**—because initialization will add the domain automatically.

If your tenant has no user configured, the initialization process will prompt you to create the initial local administrator.

```
Please choose username for initial local admin: admin
Please choose password for initial local admin:
Please choose password for initial local admin (confirm):
```

If your tenant already has a user configured, you will be prompted instead to enter the username and password for that user.

Once DSV accepts these credentials, you are ready to begin using the DSV CLI to access secrets in your organization’s Secret Server vault, and to perform operations on those secrets.

The [Commands Overview](..\05-cli-overview\index.htm), [Commands Examples](..\06-cli-examples\index.htm), and [CLI Reference](..\07-cli-ref\index.htm) will get you started.
