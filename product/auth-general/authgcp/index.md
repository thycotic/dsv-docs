[title]: # (Authentication: GCP)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (5600)

# Authentication Google Cloud Platform (GCP)

DevOps Secrets Vault provides two ways to authenticate using GCP.  One is through a Google service account and the other is through Google Compute Engine (GCE) metadata. 

## Google Service Account Authentication

To setup GCP authentication using service accounts in DSV, a GCP service account must be provided that DSV can use as the authentication provider.  This service account must be assigned to the project you are working in, have the role **Service Account Key Admin** so that it can issue and manage service account tokens, and a key must be generated.

These steps can be done programatically, but we will use the GCP Console.

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


The Goolge API for IAM must be enabled.  To do this in the Google Console, go to the relevant project and on the left nav, hover **APIs & Services** then select **Library**.

![](./images/spacer.png)

![](./images/iamapi.png)

![](./images/spacer.png)

In the search, type `Identity and Access` and in the results, select the **Identity and Access Management (IAM) API**.  Click **Enable**.

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


Setup the DSV authentication provider.  Create a json file named `auth-gcp.txt` with the following format, substituting the `dsv-svc` service account values in the key file you downloaded from the GCP console.

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

Now `thy config read --encoding yaml` should include the GCP provider named "gcloud".

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
- ID: bq4ce17cj2bc72qun8vg
name: gcloud
properties:
clientEmail: dsv-svc@myfirstproject-273119.iam.gserviceaccount.com
privateKey: |
-----BEGIN PRIVATE KEY-----
XXXXXXXXXXXXXXXXXXXxxxxxxxEFAASCBKcwggSjAgEAAoIBAQCyPQf3SQk86VD8
2RKH+vDBx+zG9gMEbFmFYypOeRjmizAMx8MfhWe6wQGHp3KgazAr7T/sRhEwbcUk
a3ATDFGHH/tuNgksi5e1StlThxgBz2q4Kc4W2ZeyZ3oTbQiffoVpziH3CpxpoI6j
a2WYdBD3B/gMxTFhFqJ6iRkG/2ywSa6tmuv+jxIi4StSE9TyTA2XQZw9qiwEQ48A
cCvJqiznpFRMMocz73ys6L0vik2JdVyQpm5APBbZcyIfGY8HlCxzgdhd5MJC+fXV
NnsGH2NVZVIzlCjuFLMBy+NvWWmisLFmCtQjQPHA1UtIp7iEK09XGB4AXx94bm0H
ySbIlJj7AgMBAAECggEATdD4e91+s4G3spSBEy4bU7cZ6Gl6usElOmenjlgvZ2Po
SAQk68ueFHp0VQnlsSTrBJqRuHGEyqx6ECL57M8JfyGW77CWw8R0KRnzFRUbhZrN
YHcb+3znTmP/96A4Tg36aE2vJYCT9ke7TpyyX+N4jqmDgevL2bP8ntvhOd1lUfc1
QWaUVE5TS1gCWkEMgZaGsCfSbRlCOr0uBBIRfO7MAlSyaK6IJpnuIuhpoNV3Zch2
77L3O9qXXXXXXXXXXXXXXxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxy/NVrspEC
JYMkF4ablf96UqIVVSs1VsZrPgQFauzpS7yXFQAq9QKBgQDzBxBzHbMg8Vi8B7P/
wtU2JJ3CSumMMLB/BNcZksRrG+2z9sz9T1FS4EBbnTGvOHl0nIythMuY3ZFkye5p
lJkZRD3YX0aNOShGakPxEf2OqYkj7pkBdhilNrz3OnnjQoz4VcAsadUGmwLAhv2b
/WLfRHgDwhyvhF9tuDRdALodVwKBgQC7wKHafPn0Vj6sOiUmjvPIDFRVO0kFQFGc
oSxE2culMxY3I4Dh8fLGNLCt5cJt/+Nsu2huHIgYVRdoQcTq/vb3gePj5lwauLYW
vEm0KyyLzLH57kZa4bDE36A5IaoZ7ESNcjcps84xYuUgd81fVkVt4qThUCo0X/VI
ACUiTqz2/QKBgQCn4ydv/wJyLYhZTRECDLxyDNWXFV1F5ZToCpX2KrfaLo8FleeC
zrqlgBm1sGBUZbUx47wjWuuzjM0WTZGQCoHBPK1kvlzkzqmOC3coIH+DgIcm9Xtp
0QWxjKD6QcFWR/FO1R5PEEWDrK44BolIq8ET8B7gqcZbUh0ClRBHd2sbPQKBgFmx
MKD7yzzaZp5IOK8u427R1QfShpOnolU8+bT6hrqoqRg2Mb++ocfmK/EnLbb242Jy
NPVFVA6rt77qjHPm0Xxz5LZeuelaDELYOC2F4oX2h59qINoRryyd2CDy4Bv6LtWT
lp6pcvtMz0CvesDsqcZQ24t3jHHw1XBMAXFePGrRAoGAdOHAN57rA/R9IuI0c92f
BZ58So0w5Yi5uEAm2hNGKFObQrKa6oABaGv3ZWx1lEuv2rKuBFAQLV6PE+XU2txr
7Cd/4vkowChgBbaUxZpVVz5SQj+nt/SW1esdNlEYxdj55r+/i8tpUDvCHHdVNMZ1
vkj7m5V8HCmnPjeic+d1OmI=
-----END PRIVATE KEY-----
privateKeyId: 9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx3
projectId: myfirstproject-273119
tokenUri: https://oauth2.googleapis.com/token
type: service_account
type: gcp
tenantName: company
```

Now the service account that is going to access DSV is required.  For this example, we will name this account `client-svc` The setup in GCP is the same as above for the `dsv-svc` account except that when the role is assigned, it must be **Service Account Token Creator** so that this account can request tokens.  Also, after generating the key, make sure to save the file to the local machine that will access DSV and note the location.

In the DSV CLI, create a user called `gcp-test` referring to the `client-svc` service account with `gcloud` as the authentication provider using `thy user create --username gcp-test --provider gcloud --externalid client-svc@myfirstproject-273119.iam.gserviceaccount.com`


```json
{
  "cursor": "",
  "data": [
    {
      "created": "2020-04-04T17:56:33Z",
      "createdBy": "users:thy-one:admin@company.com",
      "externalId": "client-svc@myfirstproject-273119.iam.gserviceaccount.com",
      "id": "d6a8e1e5-5554-4fc8-a4ca-1c1a653f9095",
      "lastModified": "2020-04-04T17:56:33Z",
      "lastModifiedBy": "users:thy-one:admin@company.com",
      "provider": "gcloud",
      "userName": "gcp-test",
      "version": "0"
    }
  ],
  "length": 1,
  "limit": 25
}
```
Set an environmental variable named GOOGLE_APPLICATION_CREDENTIALS to the path of the key file for `client-svc` that was just downloaded.

In Linux or Mac, this might look like:
```bash
export GOOGLE_APPLICATION_CREDENTIALS="/home/user/Downloads/[FILE_NAME].json"
```
Windows Powershell
```PC
$env:GOOGLE_APPLICATION_CREDENTIALS="C:\Users\username\Downloads\[FILE_NAME].json"
```
Windows Command Line
```CMD
set GOOGLE_APPLICATION_CREDENTIALS="C:\Users\username\Downloads\[FILE_NAME].json"
```

After creating the User, modify the config to give that User access to the default administrator permission policy.

> NOTE: Adding a user to the admin policy is not security best practices.  This is for example purposes only.  Ideally,  you would create a separate policy for this AWS user with restricted access.   For details on limiting access through policies, see the [Policy](../product/cli-ref/policy.md) section.

```BASH
thy config edit --encoding yaml
```

Add *gcp-test* as a User subject to the **Default Admin Policy**. Third party accounts must be prefixed with the provider name; in this case, the fully qualified username would be *glcoud:gcp-test*.

(Only the Admin policy is shown from the config file)

```yaml
description: Default Admin Policy
effect: allow
id: xxxxxxxxxxxxxxxxxxxx
meta: null
resources:
- <.*>
subjects:
- users:<thy-one:admin@company.com|gcloud:gce-test>
```

In the CLI, run `thy init` filling out the proper values and selecting (6) GCP (federated) when prompted.

```BASH
Please enter auth type:
       (1) Password (local user)(default)
       (2) Client Credential
       (3) Thycotic One (federated)
       (4) AWS IAM (federated)
       (5) Azure (federated)
	(6) GCP (federated)

```

