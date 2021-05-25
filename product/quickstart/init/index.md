[title]: # (Initialize the CLI)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2300)
[display]: # (toc/search/content/all/none/print)

# Initialize the CLI

## Required Information

DSV CLI initialization presents you with a series of prompts and options. If you are the **initial administrator** who setup the tenant, then you will have the required information from signing-up. If you are not the initial administrator, you will need the collect this information from that person:

* tenant
* domain
* local or federated user, and if federated, which authentication provider.
* credentials - username or access key, password, or secret key.

## Video Guide

<div style="padding:56.25% 0 0 0;position:relative;">
<iframe src="https://player.vimeo.com/video/490936892/" style="position:absolute;top:0;left:0;width:100%;height:100%;" frameborder="0" title="Initialize the CLI" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div><script src="https://player.vimeo.com/api/player.js"></script>

## CLI Setup Guide

1. Begin setup with the `dsv init` command. This will start a workflow.
1.  ```yaml
    Please enter your tenant name:
    ```
    Enter your tenant name. The tenant name was provided to the initial administrator by Thycotic when you set up your account.
    >**NOTE:** You need only enter your tenant name, ie, just *example* not *example.secretsvaultcloud.com*, because the domain is set by region and that is covered in the next question:
1. ```yaml
    Please choose domain:
        (1) secretsvaultcloud.com (default)
        (2) secretsvaultcloud.eu
        (3) secretsvaultcloud.com.au
    ```
    
    Select the domain. Your domain is based on the server location that was chosen during provisioning: US, EU, or AU.
    >**NOTE:** In all of the selections with numbered choices, the first choice is marked *(default)* because that is the selection if you simply hit "enter" without entering a number.
1. ```yaml
    Please enter store type:
        (1) File store (default)
        (2) None (no caching)
        (3) Pass (linux only)
        (4) Windows Credential Manager 
            (windows only)
    ```
    Choose a location for **credential storage**. 
    * Select *(1) File store (default)* to keep the credentials in a configuration file. If you select this, DSV prompts for the file location.
    * Select *(2) None (no caching)* to omit storing the credentials. With this option active, DSV requires authentication with every command.
    * Select *(3) Pass (linux only)* to use [Linux pass](https://www.passwordstore.org/) for encrypted storage.
    * Select *(4) Windows Credential Manager (windows only)* to use [Windows Credential Manager](https://support.microsoft.com/en-us/help/4026814/windows-accessing-credential-manager) to store credentials.
1. ```yaml
    Please enter auth type:
        (1) Password (local user)(default)
        (2) Client Credential
        (3) Thycotic One (federated)
        (4) AWS IAM (federated)
        (5) Azure (federated)
        (6) GCP (federated)
        (7) OIDC (federated)
    ```

    Select an **authentication type**.
    * Select *(1) Password (local user) (default)* to authenticate by username and password.
    * Select *(2) Client Credential* to authenticate by Client ID and Client Secret authentication. This option supports use of DSV commands by applications.
    * Select *(3) Thycotic One (federated)* to authenticate using Thycotic's access manager.
        >NOTE: The person who signed up for DevOps Secrets Vault is the *initial administrator* and is automatically setup using Thycotic One. If this is you, then select this option.  This enables you to reset the password if it is ever lost and/or setup up 2FA if desired. It is up to the customer to then decide if all other users are local or federated through one the available providers.
    * Select *(4) AWS IAM (federated)* to authenticate as a trusted Identity Access Management Role or User.
    * Select *(5) Azure (federated)* to authenticate as a trusted Azure Managed Service Identity (MSI).
    * Select *(6) GCP (federated)* to authenticate as a trusted Google Service Account.
    * Select *(7) OIDC (federated)* to authenticate through Thycotic One to an external IDP using the OIDC protocol.
1. ```yaml
    Please enter cache strategy for Secrets:
        (1) Never (default)
        (2) Server then cache
        (3) Cache then server
        (4) Cache then server, but allow 
            expired cache if server unreachable
    ```

    Choose a **cache strategy for Secrets**. The choice here depends on your organization's security, network connectivity, performance, and systems availability.
    > **Note**: *Server* refers to your DSV tenant and *cache* refers to storage on the local machine with the CLI installed.
    * Select *(1) Never (default)* to never cache Secrets. Every credential request requires an API call.
    * Select *(2) Server then cache* to make an API call every time. If notaccessible, then the cached Secret is used.
    * Select *(3) Cache then server* to use the cached Secret unless it has expired, in which case an API call is made.
    * Select *(4) Cache then server, but...* If the cached Secret has expired, an API call is made for the Secret.  If the API call fails, then the expired cached Secret is used.
1. ```yaml
    Please enter username 
    for tenant "example": admin@company.com

    Please enter password:*********

    Thycotic One authentication 
    provider name (default thy-one): thy-one
    ```
    You will be prompted for your credentials and authentication provider.  
    * For the initial administrator, they will be the username and password that you setup in Thycotic One during the sign-up, with the username often your email address. The authentication provider will be the default, **thy-one**.
    * Local users will not need to specify an authentication provider.

That completes setup. You can begin using the DevOps Secrets Vault Command Line Interface to [create your first secret](../secrets/index.md)
