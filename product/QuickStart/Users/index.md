[title]: # (Create Users)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2400)

#Creating local Users

With the first Secrets created, the next step is to create Users or Roles that will access those secrets.

For this quick-start guide, we will create two users - a local User and a Thycotin One User.  

First, a local DSV User, designated with their email address `local@company.com` (though the email address is not required).   

```BASH
thy user create --username local@company.com --password BadP@ssword
```

Second, a Thycotic One User will be created in DSV.  Here a valid email address is required as the username.

```BASH
thy user create --username thyoneuser@company.com --provider thy-one
```

The user will receive an email with a link to both confirm their email address and setup a password.