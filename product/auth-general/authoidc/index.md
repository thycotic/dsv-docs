[title]: # (Authentication: OIDC)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5700)

# Authentication: OIDC


Use `thy config auth-provider search --encoding yaml` to see your current authentication settings.

The initial auth settings after your tenant is provisioned should look like this:

```yaml
data:
- created: "2020-04-27T18:04:52Z"
  createdBy: ""
  id: bqjhth447csc72i4sm8g
  lastModified: "2020-04-27T18:04:52Z"
  lastModifiedBy: ""
  name: thy-one
  properties:
    baseUri: https://login.thycotic.com/
    clientId: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  type: thycoticone
  version: "0"
length: 1
limit: 25
```

# OIDC Providers

Any OIDC compliant authentication provider should be configurable to work with Thycotic One and DevOps Secrets Vault. Documented integrations are below.

## Common Steps

For all OIDC authentication providers you will need to get their provider URL, client id, and client secret. You will need to set in the authentication provider the callback URL that it will redirect to once authentication is complete.  

To get your callback URL:

1. Sign into the [cloud manager portal](https://portal.thycotic.com) and go to Manage->Teams and click on Organizations for your team.
2. Click on **Auth Providers** and then click the **New** button. This will open a dialog up. 
3. Give it a name and copy the Callback URL provided. Do not save or cancel, you will be coming back to fill out the rest of the fields.

![](./images/spacer.png)

![](./images/azure-ad-cmsetup.png)

![](./images/spacer.png)




