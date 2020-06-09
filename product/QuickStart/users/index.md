[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2500)

# Creating Users

With the first Secrets created, the next step is to create Users or Roles that will access those secrets.

For this quick-start guide, we will create two users - a local User and a Thycotic One User.  

First, a local DSV User, designated with their email address `local@company.com` will be created.  For local users, an email address is not required.

```BASH
thy user create --username local@company.com --password BadP@ssword
```

Second, a Thycotic One User will be created in DSV.  Here a valid email address is required as the username.

```BASH
thy user create --username thyoneuser@company.com --provider thy-one
```

The user will receive an email with a link to both confirm their email address and setup a password.

![Thy-One Email](./images/thyoneemail.png)

Once the Thycotic One User clicks that link and sets a password, they will be ready to authenticate to DSV.

The local and Thycotic One users can then, on their own machines, download the CLI and start the `thy init` process.  The admin will have to provide the local user with their password, and both of them with the DSV tenant name and domain (region).

The process is here [Initializing the CLI for the first time](./init/index.md)

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

The local user will select *(1)* and enter their username and password.  The Thycotic One user will select *(3)* and enter their email, Thycotic One password, and for the provider name (simply hit <enter> to default to *thy-one*).

At this point, the users are created and able to authenticate to DSV (they can comfirm with the command `thy auth` and get a token), however, they will not have permission to access anything yet because DSV defaults to *deny all*.  In the next step, the admin will create policies granting permission to these users.