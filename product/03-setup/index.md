[title]: # (Setup DevOps Secrets Vault)
[tags]: # (,)
[priority]: # (1300)

# Setup DevOps Secrets Vault

## Overview

DevOps Secrets Vault supports diverse infrastructures and security models, allowing you to:

* choose how DSV stores credentials
* determine the type of authentication
* select the cache strategy for secrets

## Profiles

DSV records your choices in a **profile**. You can:

* create, name, save, and use more than one profile
* specify the default profile for commands
* on a per command basis, specify the profile to be used

You make these choices during setup, which you perform using the command `thy init`.

## The Simplest Case: Setup with Default Settings

The simplest possible case is to use `thy init` without flags. This will initialize DSV with default settings, so that you do not make any of the above choices and operate the CLI using a single, un-named profile.

To obtain such a default setup, run `thy init`. DSV prompts for your tenant name.

```bash
thy init
Please enter tenant name: acme
```

Specify the tenant name Thycotic provided when setting up your organization’s account.

* Do not specify the full domain—just `acme`, not `acme.secretsvaultcloud.com`—because initialization will add the domain automatically.

If your tenant has no user configured, DSV will prompt you to create the initial local administrator.

```
Please choose username for initial local admin: admin
Please choose password for initial local admin:
Please choose password for initial local admin (confirm):
```

If your tenant already has a user configured, DSV will instead prompt you to enter the username and password for that user, which Thycotic will have provided to you during provisioning.

Once DSV accepts these credentials, you are ready to begin using the DevOps Secrets Vault Command Line Interface to administer secrets in your organization’s secure, cloud-hosted secrets vault.

## The More Likely Case: Setup with Custom Settings

Although setup with defaults would get you started, you probably want to specify the type of credentials store, the authentication type, and the cache strategy for secrets.

To configure DSV with your custom choices for these settings, use this command:

`thy init --advanced`

When you use the `--advanced` flag, DSV presents you with a series of questions and choices.

> **TIP:** The `--profile` flag allows you to set the name of the profile setup will create, and you can use the `--profile` and `--advanced` flags at the same time. For example:
>
> `thy init --advanced --profile [name]`
>
> This is useful if you decide later to have multiple profiles, because you won’t need to run this setup again to specify the profile name; you will only need to run setup to create the additional named profiles.

All setups begin with DSV asking your tenant name.

```bash
thy init --advanced
Please enter tenant name: example
Please enter domain (default:secretsvaultcloud.thycotic.com):
```

Specify the tenant name Thycotic provided when setting up your organization’s account.

* Remember, you need only enter your tenant name, for example, just `acme` not `acme.secretsvaultcloud.com`, because initialization adds the domain automatically.

Next, DSV prompts you about **credential storage**.

```bash
Please enter store type:
        (1) File store (default)
        (2) None (no caching)
        (3) Pass (linux only)
        (4) Windows Credential Manager (windows only)
```

Select `(1) File store (default)` to keep the credentials in a configuration file. If you select this, DSV prompts for the storage location.

Select `(2) None (no caching)` to avoid storing the credentials. With this option active, DSV requires periodic re-authentication.

Select `(3) Pass (linux only)` to use [Linux pass](https://www.passwordstore.org/) for encrypted storage.

Select `(4) Windows Credential Manager (windows only)` to use [Windows Credential Manager](https://support.microsoft.com/en-us/help/4026814/windows-accessing-credential-manager) to store credentials.

Your next selection concerns the **type of authentication**.

```bash
Please enter auth type:
        (1) Password (default)
        (2) Client Credential
        (3) AWS IAM (federated)
        (4) Azure (federated)
```

Select `(1) Password (default)` to authenticate by username and password.

Select `(2) Client Credential` to authenticate by Client ID and Client Secret authentication; this supports use of DSV commands by applications.

Select `(3) AWS IAM (federated)` to authenticate as a trusted Identity Access Management Role or User.

Select `(4) Azure (federated)` to authenticate as a trusted Azure Managed Service Identity  (MSI).

The [Authentication: General](..\04-authent-gen\index.htm) and [Authentication: Azure or AWS](..\05-authent-azure-aws\index.htm) provide details for each of these choices. 

Finally, the initialization process will prompt about the **cache strategy for secrets**. The choice here depends on your specific set of concerns around security, network connectivity, performance, and systems availability.

```bash
Please enter cache strategy for secrets:
        (1) Never (default)
        (2) Server then cache
        (3) Cache then server
        (4) Cache then server, but allow expired cache if server unreachable
```

> Note: In this context, *server* simply means the elements in the cloud that collectively manifest as though a unitary entity, not a literal server.

* DSV defaults to **never** caching secrets. Every credential request requires an API call.

* The second choice, **server then cache**, allows DSV to use a cached copy of the secret in lieu of obtaining it directly from the vault. This can help blunt the impacts of problems affecting connectivity to the server.

* **Cache then server** can substantially reduce secrets-related network traffic and API calls while also reducing the load on the server, because once DSV accesses a secret, it need not pull that secret from the server again until the cached copy expires.

* The last choice allows use of cached secrets even when they have expired, *provided* the server cannot be reached. This offers the most resilience against disruptions, but may not comport with some organization’s security policies.

Selecting the cache strategy completes setup. You can begin using the DevOps Secrets Vault Command Line Interface to administer secrets in your organization’s secure cloud-hosted secrets vault.

The [Commands Overview](..\06-cli-overview\index.htm), [Commands Examples](..\07-cli-examples\index.htm), and [CLI Reference](..\08-cli-ref\index.htm) will get you started.

