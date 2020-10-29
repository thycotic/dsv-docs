[title]: # (GCP Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6300)

# GCP Dynamic Secrets

There are two ways to generate dynamic GCP secrets:

* [Token Generation](https://cloud.google.com/iam/docs/creating-short-lived-service-account-credentials)
* [Service Account Key](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)

Token generation creates an access token that can be used as the bearer token in the GCP API. Service account key generation creates a new key on a service account in GCP and then deletes the key after the specified time to live is up.

## Setup

### Create a GCP Service Account
For setting up GCP token or key based dynamic secrets you will first need a service account in GCP. 

* Go to **Service Accounts** under **IAM & Admin** in the GCP console
* Click **Create Service Account** and grant it access to a project
* Generate a key for the service account and save it.
* Under **IAM** Assign the `Service Account Key Admin` and `Service Account Token Creator` roles to the new service account. Also give it `Storage Admin` which will be used for testing the dynamic secrets.


### Create the Base Secret

Next create a Secret in DSV with the AWS IAM user access key, secret key, and region.

Create a file named `secret_root.json` substituting your values from the service key file:

```json
{
	  "projectId": "test-project-1234",
	  "type": "service_account",
	  "privateKeyId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
	  "privateKey": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n",
	  "clientEmail": "dsv-test@test-project-1234.iam.gserviceaccount.com",
	  "clientId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
	  "tokenUri": "https://oauth2.googleapis.com/token"
}

```

Create the Secret via the CLI at a path of your choosing:

```BASH
dsv secret create --path gcp/base/svc-account --data @secret_root.json --attributes '{"type": "gcp"}'
```

## OAuth Access Token


| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| scopes                    | Array of GCP OAuth 2.0 [scopes](https://developers.google.com/identity/protocols/oauth2/scopes) for the dynamic token                                                    |
| providerType              |  `token`                                                            |

![](./images/spacer.png)

Now you need to create a Dynamic Secret, which points to the base Secret via its attributes. The Dynamic Secret doesn't have any data stored in it because data is only populated when you read the Secret.

Create an attributes json file named `secret_attributes.json' substituting your values.


```json
{
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "gcp:base:svc-account"
	},
	"providerType": "token",
	"scopes": [
            "https://www.googleapis.com/auth/devstorage.full_control"
        ]
}
```

Create a new Dynamic Secret

```BASH
dsv secret create --path dynamic/gcp/token --attributes @secret_attributes.json
```

Now anytime you read the Dynamic Secret, the data is populated with the a temporary access token that is valid for 1 hour.


```BASH
dsv secret read --path dynamic/gcp/token
```

returns a result like:


```json
{
    "id": "ba2f1fc7-c16f-4062-a216-3116d1a42545",
    "path": "dynamic:gcp:token",
    "attributes": {
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "gcp:base:svc-account"
        },
        "providerType": "token",
        "scopes": [
            "https://www.googleapis.com/auth/devstorage.full_control"
        ]
    },
    "description": "gcp dynamic token secret",
    "data": {
        "access_token": "ya29.c.Ko8ByAfMsL-...JbFloC6tOiUCOM6vXn7YNhZA",
        "expiry": "2020-04-26T22:04:32.3897188Z",
        "ttl": 3600
    }
}
```

You can validate the credentials are able to read storage buckets by making an API request with the access token in the `Authorization` header to the storage API for your project, substituing your values:

```BASH
curl -H 'Authorization: Bearer {access token}' https://storage.googleapis.com/storage/v1/b?project={project id}
```

![](./images/spacer.png)



## Service Account Key

In this example, rather than generating an OAuth token we will generate a new key in json format for the service account. This creates a new key in GCP that can be used to authenitcate with the gcloud CLI or other SDK's. Once the ttl for the dynamic secret expires the key will be removed.

>Service accounts in GCP are limited to 10 keys per account. If you exceed this you will get a 400 error reading the dynamic secret with a message of `unable to create new service account key googleapi: Error 429: Maximum number of keys on account reached., rateLimitExceeded`

>To help avoid this ensure that you keep ttl's relatively low for service account keys to ensure they get cleaned up. You can also create multiple service accounts with the same permissions in GCP and then create a base secret for each one to help spread the number of keys across service accounts.

### Create the Base Secret

For this example, we will reuse the base secret from above.  If you haven't done this already, then follow those directions to create the base secret now.


### Create the Dynamic Secret


| Attribute                 |Description                                                |
| --------------            |------------------------------                             |
| providerType              | `serviceKey`                                              |
| ttl                       | required time to live in seconds of the generated token.  |

![](./images/spacer.png)

Create or update the attributes json file named `secret_attributes.json` changing the provider type to `serviceKey` and replacing the 


```json
{
	"linkConfig": {
		"linkType": "dynamic",
            "linkedSecret": "gcp:base:svc-account"
	},
	"providerType": "serviceKey",
	"ttl": 3600
}
```
Now create the dynamic secret in the CLI using the json above.

```BASH
dsv secret create --path dynamic/gcp/secret-svc-key --attributes @secret_attributes.json
```

Now anytime you read the Dynamic Secret, the data is populated with the GCP service key.


```BASH
dsv secret read --path dynamic/gcp/secret-svc-key
```

returns a result like:


```json
{
	"linkConfig": {
		"linkType": "dynamic",
        "linkedSecret": "gcp:base:svc-account"
	},
	"providerType": "serviceKey",
	"ttl": 3600
  },
  "data": {
    "keyAlgorithm": "KEY_ALG_RSA_2048",
    "keyOrigin": "GOOGLE_PROVIDED",
    "name": "projects/test-proj-1234/serviceAccounts/dsv-test@test-prog-1234.iam.gserviceaccount.com/keys/0e4c690b713bfe0ed517ed56cba4814afd35a8ad",
    "privateKeyData": 
    {
      "client_id": "xxxxxxxxxxxxxxxxx",
      "auth_uri": "https://accounts.google.com/o/oauth2/auth",
      "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/dsv-test%40test-proj-1234.iam.gserviceaccount.com",
      "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
      "client_email": "dsv-test@test-project-1234.iam.gserviceaccount.com",
      "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADAN...iV7quFF35ILBG+w=\n-----END PRIVATE KEY-----\n",
      "private_key_id": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "token_uri": "https://oauth2.googleapis.com/token",
      "type": "service_account",
      "project_id": "test-proj-1234"
    },
    "ttl": 3600
  },
  "description": "",
  "id": "34fb64d7-18da-453d-9487-3d1c082ba372",
  "version": "0"
}
```

Copy the inner JSON of `privateKeyData` into a file and name it svc-account.json. Then using the gcloud CLI run `gcloud auth activate-service-account --key-file svc-account.json` to test the generated key is valid.  If so, you will get a reply similar to 'Activated service account credentials for: [service account email]'

After the ttl expires you can check the keys on the service account and they will be removed. Note that there may be some delay between when the ttl expires and when the key is removed from the service account.

![](./images/spacer.png)

![](./images/spacer.png)