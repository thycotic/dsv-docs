[title]: # (Azure Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6200)

# Azure Dynamic Secrets

An Azure service principal is an identity created for use with applications, hosted services, and automated tools to access Azure resources. 

These are the links to azure documentation on service principal:

* [Service principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)
* [Create Service Principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)


In order for DSV to generate dynamic Secrets, a base secret must first be created using a service principal that has permissions to manage other service principals.  Those permissions include:

* "Owner" role for the subscription scope  
* "Read and write all applications" permission in Azure Active Directory. 
*  Your account must have Microsoft.Authorization/*/Write access to assign an active directory application to a role.

These permissions can be configured through the Azure Portal, CLI tool, or PowerShell. 


## Create the Base Secret

The base Secret holds the credentials required for DSV to perform API calls to Azure to query roles and create/delete service principals.   

| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| subscription_id              | The subscription ID for the Azure Active Directory.   |
| tenant_id                    | The tenant ID for Azure Active Directory.                  |
| client_id                    |  The OAuth2 client ID to connect to Azure. Azure might list it as Application (client) ID |
| client_secret                |  The OAuth2 client secret to connect to Azure.
| environment                 |  The Azure environment. If not specified, DSV will use Azure Public Cloud. |

![](./images/spacer.png)

Create a file named `secret_base.json` substituting your values:

```json
{
    	"subscriptionId": "028b0a8e-84d2-4797-9071-456fe718248e",
		"tenantId":       "s0kkfsld-569l-491f-err4-7f4d831987fd",
		"clientId":       "f0b2cbb9-5782-42d5-8647-028b0a8e61f2",
		"clientSecret":   "tMQ5ZEP?.sjfk5435jlKJSDedDKJDKJD@]"
}
```
Create the base Secret via the CLI substituting a path of your choosing:

```BASH
thy secret create --path azure/base/api-account --data '@secret_base.json' --attributes '{"type": "azure"}' --desc "azure base credential"
```

## Dynamic Secrets
In DSV you can create dynamic Secrets from either an existing service principal or create a temporary service principal. 

>NOTE Temporary vs Existing Service Principals:  Azure does not use these terms, but DSV can either use a service principal that you have already setup (existing)  or DSV can create a service principal on the fly (temporary).

>If the Azure resources can be provided via the RBAC system and Azure roles defined in DSV then a temporary service principal is preferred. Temporary service principals are decoupled from any other service principal providing fine grained access and auditing. However, creating temporary service principals can take up to 2 minutes before fully provisioned on azure. 

>Some Azure services are unable to be provided through the RBAC system. In these cases, an existing service principal can be set up with the necessary access, and DSV can create new client secret for this service principal. Any changes to the service principal permissions affect all clients and Azure does not provide any logging with regard to which credential was used for an operation. Another limitation when using an existing service principal is that Azure limits the number of passwords for a single Application object. An error will be returned if the object size is reached. This limit can be managed by reducing the role TTL.


## Dynamic Secret for an Existing Service Principal
Create a dynamic Secret that points to the base Secret via its attributes. The dynamic Secret doesn't have any data stored in it because data is only populated when you read the Secret.

     

| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| roleName                  | Optional for informational purposes - Azure role name to be assigned to the existing service principal.  Does not change existing principal's role    |
| appId                     | Application (client) ID for an existing service principal                   |
| appObjectId               | Application Object ID for an existing service principal             |
| ttl                       | Optional time to live in seconds of the generated token. If none is specified it will default to 900 |

![](./images/spacer.png)

Create an attributes json file named `secret_attributes.json` substituting your values

```json
{
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "azure:base:api-account"
	},
	"roleName":       "Contributor",
	"appId": "08cfa61b-56ec-46c7-9780-247cfac0ef0c", 
	"appObjectId" : "69297292-8373-4e4b-8679-434439b57347",
	"ttl": 360  
}
```

Create the dynamic Secret via the CLI substituting the path of your choosing.
 
```BASH
thy secret create --path azure/dynamic/api-account --attributes '@secret_attributes.json' --desc "azure dynamic credential"
```


Now anytime you read the dynamic Secret, the data is populated with the temporary Azure access credentials.


```BASH
thy secret read --path azure/dynamic/api-account
```

Returns a result like:


```json
{
    "id": "6e7de928-5027-4afb-bbff-b3ee59f9c24f",
    "path": "dynamic:azure:sp-static",
    "attributes": {
        "appId": "08cfa61b-56ec-46c7-9780-247cfac0ef0c",
        "appObjectId": "69297292-8373-4e4b-8679-434439b57347",
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "azure:base:api-account"
        },
        "roleName": "Contributor",
        "ttl": 360
    },
    "data": {
        "appObjectId": "69297292-8373-4e4b-8679-434439b57347",
        "client_id": "08cfa61b-56ec-46c7-9780-247cfac0ef0c",
        "client_secret": "fec113b7-2d41-434c-a42f-d252c37aa2ab",
        "role": "Contributor",
        "subscription_id": "5f74ce1f-84d2-4797-9071-456fe718248e",
        "tenant_id": "00fb18e9-208a-491f-afb0-7f4d831987fd",
        "ttl": 360
    },
    "created": "2020-02-24T16:42:34Z",
    "lastModified": "2020-03-04T19:21:04Z",
    "version": "13"
}
```



## Dynamic Secret for a Temporary Service Principal 

> Note: Creating service principal and assigning role in same request takes tens of seconds (over a minute has been seen), The command has been broken down into two separate calls. In the first call the service principal will be returned along with the task id that fired in the background for role assignment. You will need to wait to use that temporary service principal or check via the Azure portal or via the DSV API (provided below)

| Attribute          | Description                                                         |
| --------------         | ------------------------------                               | 
| roleName                  | Optional for informational purposes - Azure role name to be assigned to the temporary service principal.  DSV uses role ID to assign.      
| roleId                    |  Azure role id to be assigned to the temporary service principal
| scope                     |  Azure role name to be assigned to the temporary service principal
| ttl                       | Optional time to live in seconds of the generated token. If none is specified it will default to 900. |

>Note:  Azure built-in role names and IDs can be found [here](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)

Create an attributes json file named `secret_attributes.json` substituting your values.

```json
{
    
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "azure:base:api-account"
	},
	"roleName":       "Contributor",
	"roleId":         "/subscriptions/<Azure Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
	"scope":          "/subscriptions/<Azure Subscription ID>/resourceGroups/dsv-secret-test",
	"ttl": 36000
    
}

```

Create a new Dynamic Secret via the CLI substituting the path of your choosing.
 
```BASH
thy secret create --path /azure/dynamic/api-account --attributes '@secret_attributes.json' --desc "azure dynamic credential" 
```


Now anytime you read the dynamic Secret, the data is populated with the temporary azure access credentials.

```json
{
    "id": "27a405c6-14b4-4d4b-b566-9fe23f1012c2",
    "path": "dynamic:azure:ac-api",
    "attributes": {
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "azure:base:api-account"
        },
        "roleId": "/subscriptions/5f74ce1f-84d2-4797-9071-456fe718248e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "roleName": "Contributor",
        "scope": "/subscriptions/5f74ce1f-84d2-4797-9071-456fe718248e/resourceGroups/dsv-secret-test",
        "ttl": 36000
    },
    "description": "azure root credtial",
    "data": {
        "appObjectId": "e463477c-7d90-4743-92f2-c7f44ede8ec9",
        "client_id": "105bf8e9-df73-4870-aeb9-ec0a467cc3d3",
        "client_secret": "36eb10b4-b1e7-4f7b-9c9d-f18691ef95ad",
        "role": "Contributor",
        "roleAssignmentId": "/subscriptions/5f74ce1f-84d2-4797-9071-456fe718248e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "roleAssignmentStatus": "created",
        "roleAssignmentTaskId": "task_3da0a37c-0a1c-4ebd-8829-dbe7b988b36f",
        "spObjectId": "1782611c-99c2-418b-b672-783e3cf8bd14",
        "subscription_id": "5f74ce1f-84d2-4797-9071-456fe718248e",
        "tenant_id": "00fb18e9-208a-491f-afb0-7f4d831987fd",
        "ttl": 36000
    },
    "created": "2020-02-12T20:57:44Z",
    "lastModified": "2020-03-04T19:27:45Z",
    "version": "12"
}
```

It takes some time for the temporary service principal to be created, so you can check using the Azure portal for the new service principal or use the DSV API: 
> Use the `roleAssignmentTaskId` from above response

| method                 | path                                                         |
| --------------         | ------------------------------                               | 
| GET                 | /v1/task/status/{roleAssignmentId}      |

![](./images/spacer.png)

Sample Response:
```json
{
    "taskName": "azure_role_assignment",
    "state": "SUCCESS",
    "results": null,
    "error": "",
    "createdAt": "2020-03-04T19:28:07.420285103Z"
}
```


![](./images/spacer.png)

![](./images/spacer.png)