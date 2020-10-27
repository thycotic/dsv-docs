[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2500)

# Creating Users

With the first Secrets created, the next step is to create Users or Roles that will access those secrets.

For this quick-start guide, as the intial admin, we will create two users - a local User and a Thycotic One User.  

## Local Users

* Create a user and assign credentials using the following format:

    **```dsv user create --username local@company.com --password userpassword```**

    >**Note**: For local users, the email address serves only as the username.

## Thycotic One User

1. Create a user and assign credentials using the following format:
    
    **```dsv user create --username thyoneuser@yourorganization.com --provider thy-one```** 
1. The user will receive an email with a link to both confirm their email address and setup a password.

    ![Thy-One Email](./images/thyoneemail.png)

1. Once the Thycotic One User follows the link and sets a password, they will be ready to authenticate to DSV.

# Local User and Thycotic One User Authentication

The local and Thycotic One users can then, on their own machines, download the CLI and start the `dsv init` process.  The admin will have to provide the local user with their password, and both of them with the DSV tenant name and domain (region).

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

The local user will select *(1)* and enter their username and password.  The Thycotic One user will select *(3)* and enter their email, Thycotic One password, and for the provider name simply hit `enter` to default to *thy-one*.

The local user should change their password immediately as a best practice because the admin knows it and had to transfer it to them somehow.  The command is:

```bash
dsv auth changepassword
```

At this point, the users are created and able to authenticate to DSV (they can confirm with the command `dsv auth` and get a token), however, they will not have permission to access anything yet because DSV defaults to *deny all*.  In the next step, the admin will create policies granting permission to these users.