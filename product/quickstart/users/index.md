[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2500)

# Creating Users

With the first Secrets created, the next step is to create Users or Roles that will access those secrets.

For this quick-start guide, as the intial admin, we will create a local User. To use other authentication methods, see [authentication](../../auth-general/index.md).

## Create Local Users

Create a user and assign credentials using the following format:

**```dsv user create --username local@company.com --password userpassword```**

>**Note**: For local users, the email address serves only as the username.

## Local User Authentication

The local user can then, on their own machine, download the CLI and start the `dsv init` process. The admin will have to provide the user with their password, DSV tenant name, and domain (region).

The process is here: [Initializing the CLI for the first time](../init/index.md)

When they get to the **Please enter auth type:** 

```BASH
Please enter auth type:
        (1) Password (local user)(default)
        (2) Client Credential
        (3) Thycotic One (federated)
        (4) AWS IAM (federated)
        (5) Azure (federated)
        (6) GCP (federated)
        (7) OIDC (federated)
```

The user will select *(1)* and enter their username and password. The user should change their password immediately as a best practice. The command to change the password is:

```bash
dsv auth changepassword
```

At this point, the users are created and able to authenticate to DSV (they can confirm with the command `dsv auth` and get a token), however, they will not have permission to access anything yet because DSV defaults to *deny all*.  In the next step, the admin will create [policies](../policies/index.md) granting permission to these users.