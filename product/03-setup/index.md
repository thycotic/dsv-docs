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

You make these choices during setup, which you perform using the command: `thy init`

## The Simplest Case: Setup with Default Settings

The simplest possible case is to use `thy init` without flags. This will initialize DSV with default settings, so that you do not make any of the above choices and operate the CLI using a single, un-named profile.

Run `thy init` to obtain such a default setup. DSV prompts for your tenant name.

```bash
thy init
 Please enter tenant name: acme
```

Specify the tenant name Thycotic provided when setting up your organization’s account.

* Do not specify the full domain; use `acme` instead of `acme.secretsvaultcloud.com` because initialization will add the domain automatically.

When you sign up for a trial and choose your DSV tenant name you are also prompted to create a Thycotic One user. By default the provisioning process links your Thycotic One account and your DSV tenant, so the initial admin you sign in with will be your Thycotic One username and password.

```bash
Please enter username for tenant "acme":
 Please enter password:
```

Once DSV accepts these credentials, you are ready to begin using the DevOps Secrets Vault Command Line Interface to administer secrets in your organization’s secure, cloud-hosted secrets vault.

## The More Likely Case: Setup with Custom Settings

Although setup with defaults would get you started, you probably want to specify the type of credentials store, the authentication type, and the cache strategy for secrets.

To configure DSV with your custom choices for these settings, use this command:

`thy init --advanced`

When you use the `--advanced` flag, DSV presents you with a series of questions and choices.

* **TIP:** The `--profile` flag allows you to set the name of the profile setup will create; you can use the `--profile` and `--advanced` flags at the same time. For example:

  `thy init --advanced --profile [name]`

  This is useful if you decide later to have multiple profiles, because you won’t need to run this setup again to specify the profile name; you will only need to run setup to create the additional named profiles.

All setups begin with DSV asking your tenant name and domain. Your domain is based on the server location was chosen during provisioning: United States, European Union, or Australia.

```bash
thy init --advanced
 Please enter tenant name: example
 Please choose domain:
        (1) secretsvaultcloud.com (default)
        (2) secretsvaultcloud.eu
        (3) secretsvaultcloud.com.au
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

Select `(4) Azure (federated)` to authenticate as a trusted Azure Managed Service Identity (MSI).

The [Authentication: General](../04-authent-gen/index.md) and [Authentication: Azure or AWS](../05-authent-azure-aws/index.md) articles provide details for each of these choices.

Next, the initialization process will prompt about the **cache strategy for secrets**. The choice here depends on your specific set of concerns around security, network connectivity, performance, and systems availability.

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

Finally, you will be prompted for your username, password, and Thycotic One provider name. By default your Thycotic One authentication provider name should be **thy-one**.

* Administrators can create local users not tied to an external authentication system. When authenticating as such a user, leave the prompt for the Thycotic One provider name blank.

```bash
Please enter username for tenant "acme":
 Please enter password:
 Thycotic One authentication provider name (leave blank for local users): thy-one
```

That completes setup. You can begin using the DevOps Secrets Vault Command Line Interface to administer secrets in your organization’s secure cloud-hosted secrets vault.

The [Commands Overview](../06-cli-overview/index.md), [Commands Examples](../07-cli-examples/index.md), and [CLI Reference](../08-cli-ref/index.md) articles will get you started.  
  

