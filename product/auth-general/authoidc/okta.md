[title]: # (Okta Example)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5720)

#Okta Identity Provider Example

This example uses Okta as a OIDC identity provider.

1. Get the callback URL from Thycotic's cloud manager portal following the directions at [Authentication:OIDC](./index.md)
2. Login to your Okta Admin console. 
3. From the top, select **Applications**
4. Select **Add Apliction**
5. Select **Create New App**.  A wizard will open
6. For platform, select **Web** from the dropdown and the **OpenID Connect** radio button.  Click **Create**

![](./images/spacer.png)

![](./images/oktawizmethod.png)

![](./images/spacer.png)

7. On the resulting screen, provide an **Application name** and optional logo.  Enter the Thycotic callback URL in the box labeled **Login redirect URIs**.  Click **Save**.
