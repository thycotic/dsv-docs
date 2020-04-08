[title]: # (Authentication: GCP)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5600)

# Authentication Google Cloud Platform (GCP)

DevOps Secrets Vault provides two ways to authenticate using GCP.  One is through a Google service account and the other is through Google Compute Engine (GCE) metadata. 

## Google Service Account Authentication

To setup GCP authentication in DSV, a GCP service account must be provided that DSV can use as the authentication provider.  This service account must be assigned to the project you are working in, have the role **Service Account Key Admin** so that it can issue and manage service account tokens, and a key must be generated.

These steps can be done programmitically, but we will use the GCP Console.

In the GCP Console Home page, go to your project, hover **IAM & Admin**, and then click **Service Accounts**.  

![](./images/spacer.png)

![](./images/gcpiam.png)

![](./images/spacer.png)

At the top, click **CREATE SERVICE ACCOUNT**.  

For the first step, enter an account name.  We will use `dsv-svc` in this example.  Click **CREATE**.

In the second step, click the dropdown arrow in the **Select a role** box, then type `service account key admin` in the filter and select **Service Account Key Admin**.  Then click **Continue**.

![](./images/spacer.png)

![](./images/servicekeyadmin.png)

![](./images/spacer.png)

In the third step, click **CREATE KEY** and when the option to generate a file slides in from the right, select **json** and click **CREATE**.  A file will be downloaded that will have all the information needed to setup the DSV authentication provider.

![](./images/spacer.png)

![](./images/privatekey.png)

![](./images/spacer.png)


The Goolge API for IAM must be enabled.  To do this in the Google Console, go to the relevant project and on the left nav, hover **APIs & Services ** then select **Library**.

![](./images/spacer.png)

![](./images/iamapi.png)

![](./images/spacer.png)

In the search, type **Identity and Access** and in the results, select the **Identity and Access Management (IAM) API**.  Click **Enable**.

![](./images/spacer.png)

![](./images/iamapienable.png)

![](./images/spacer.png)

Go back to the terminal (Devops Secrets Vault CLI)

Use `thy config read --encoding yaml` to see your current configuration.  The initial config will look similar to this:

```yaml
permissionDocument:
- actions:
- <.*>
conditions: {}
description: Default Admin Policy
effect: allow
id: xxxxxxxxxxxxxxxxxxxx
meta: null
resources:
- <.*>
subjects:
- users:<thy-one:admin@company.com>
settings:
authentication:
- ID: xxxxxxxxxxxxxxxxxxxx
name: thy-one
properties:
baseUri: https://login.thycotic.com/
clientId: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
clientSecret: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
type: thycoticone
tenantName: company
```


Setup the DSV authentication provider.  Create a json file named `auth-gcp.txt` with the following format, substituting the `dsv-svc` service account values in the json file you downloaded from the GCP console.

```json
{
"name": "gcloud",
"type": "gcp",
"properties": {
	  "ProjectId": "{project-id}",
	  "type": "service_account",
	  "PrivateKeyId": "{private-key-id}",
	  "PrivateKey": "-----BEGIN PRIVATE KEY-----{private-key}-----END PRIVATE KEY-----\n",
	  "ClientEmail": "{clientemail}",
	  "TokenURI": "https://oauth2.googleapis.com/token"
	}
}
```
In the DSV CLI, run `thy config auth-provider create --data @auth-gcp.txt` to create the GCP authentication provider.

Now the service account that is going to access DSV is required.  The setup in GCP is the same as the DSV account except that when the role is assigned, it must be **Service Account Token Creator** so that this account can request tokens.  Also, after generating the key, make sure to save the file to the local machine that will access DSV and note the location.

Create a user in DSV using this service account using `thy user create --username {username} --provider gcloud --externalid {your client email}`

Set an environmental variable name GOOGLE_APPLICATION_CREDENTIALS to the path of the key file

Finally, in the CLI, run `thy init` filling out the proper values and selecting (6) GCP (federated) when prompted.



