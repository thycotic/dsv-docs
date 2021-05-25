[title]: # (GCP Example)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5710)

## Google Identity Provider Example

## Configure Auth Providers

This example uses the Google Cloud Identity service.

1. Get the callback URL from Thycotic One following the directions at [Authentication:OIDC](./index.md)

2. Go to the [Google Cloud API Console](https://console.cloud.google.com/apis/dashboard) and select a project if needed.

3. Select Credentials and click Create Credentials and click OAuth Client ID.

4. Choose **Web Application**

5. Enter the information, setting the Authorized origin as `https://portal.thycotic.com/` and Authorized redirect as the callback URL copied from the Thycotic cloud manager portal. Follow the instructions to add these URL's to the OAuth consent screen.

![](./images/spacer.png)

![](./images/setupgcpapp.png)

![](./images/spacer.png)

6. Save and copy the client id and client secret from the dialog into the credentials create dialog in Cloud Manager. Your **Provider URL** in cloud manager should be set to `https://accounts.google.com`

![](./images/spacer.png)

![](./images/setupcmprovider.png)

![](./images/spacer.png)


7. Save the credential create dialog in cloud manager and go back to Organizations. Click Credentials and then edit your Credential. This is what is used by DSV to connect to the Thycotic One identity provider for authentication. 

8. Verify that there is a **Post-Login** Redirect URI for `http://localhost:8072/callback`. If there isn't, add one. This is the callback used when logging into DSV with the CLI.


![](./images/spacer.png)

![](./images/cmcredentials.png)

![](./images/spacer.png)
