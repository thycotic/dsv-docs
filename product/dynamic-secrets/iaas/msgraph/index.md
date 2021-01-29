[title]: # (Azure MS Graph Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6250)

# Microsoft Graph Dynamic Secrets

To create dynamic secrets for Azure Microsoft Graph:

* Create an [Azure Service Principal](azure-sp.md) for Microsoft Graph.
* Create a base secret.
* Create the dynamic secret.

## Create the Base Secret

The base Secret holds the credentials required for DSV to perform API calls to Azure to query roles and create/delete service principals.   

| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| subscriptionId              | Required - The subscription ID holding the resources you wish to access using Azure Active Directory.   |
| tenantId                    | Required - The tenant ID for Azure Active Directory. Azure lists it in places as "Directory (tenant) ID"                  |
| clientId                    |  Required - The OAuth2 client ID to connect to Azure. Azure lists it in places as "Application (client) ID" |
| clientSecret                |  Required - The OAuth2 client secret to connect to Azure.
| environment                 |  Optional - The Azure environment. If not specified, DSV will use Azure Public Cloud. |

![](./images/spacer.png)

Create a file named `secret_base.json` substituting your values:

```json
{
    	"subscriptionId": "yoursubscriptionId",
		"tenantId":       "yourtenantId",
		"clientId":       "yourclientId",
		"clientSecret":   "yoursecret"
}
```
Create the base Secret via the CLI substituting a path of your choosing:

```BASH
dsv secret create --path azure/base/api-account --data '@secret_base.json' --attributes '{"type": "azure"}' --desc "azure base credential"
```

## Dynamic Secret for a Temporary Service Principal 

| Attribute          | Description                                                         |
| --------------         | ------------------------------                               | 
| appRoleId                  | Required - The id of the appRole (defined on the resource service principal) to assign to the client service principal.      
| ResourceID                 | Required - The id of the resource servicePrincipal (the API) which has defined the app role (the application permission).
| ttl                       | Optional - Time to live in seconds of the generated token. If none is specified it will default to 900. |

1. Create an attributes json file named `secret_attributes.json` substituting your base secret path, resourceID, and AppRoleID.

```json
{
		"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "azure:base:msgraph"
	},
	"ttl": 360,
	"resourceId": "resourceID",
	"appRoleId": "appRoleId",
	"msApiType": "msgraph"
    }
}

```
2. Create a new Dynamic Secret via the CLI substituting the path of your choosing.
 
```BASH
dsv secret create --path /azure/dynamic/api-graph --attributes '@secret_attributes.json' --desc "azure dynamic credential" 
```
3. Now anytime you read the dynamic Secret, the data is populated with the temporary azure access credentials.

```json
{
    "id": "8247cc11-7465-49de-9d49-959f1d2e7e39",
    "path": "dynamic:azure:ac-graph",
    "attributes": {
        "appRoleId": "c6d6abd5-4021-4d46-8f18-xxxxxxxxxxx",
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "azure:base:api-graph"
        },
        "msApiType": "msgraph",
        "resourceId": "8c828069-ab9c-4a3b-b30e-xxxxxxxxxxxx",
        "ttl": 360
    },
    "description": "azure root credential",
    "data": {
       
        "clientId": "xxxxxxxx-eaa5-4c82-a177-f526742f8881",
        "clientSecret": "secret key",
        "displayName": "dsv-7c89bba6-d61e-4de8-9c10-a4735f8eebff",
        "servicePrincipalId": "xxxxxxx-2e29-4bad-a908-a5cf0c7eaebb",
        "subscriptionId": "your subscriptionId",
        "tenantId": "your tenantId"
    },
    "created": "2020-12-13T03:31:07Z",
    "lastModified": "2021-01-12T19:50:24Z",
    "createdBy": "users:user1",
    "lastModifiedBy": "users:user1",
    "version": "15"
}
```
