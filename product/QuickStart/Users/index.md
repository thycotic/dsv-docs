[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2400)

#Creating Users

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

