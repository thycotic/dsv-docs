[title]: # (Azure Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6200)

# Azure Dynamic Secrets

DevOps Secrets Vault relies on Azure service principals to provide Dynamic Secrets.  

In order for DSV to generate dynamic Secrets, a base secret must first be created using a service principal that has permissions to manage other service principals.  Those permissions include:

* "Owner" role for the subscription scope  
* "Read and write all applications" permission in Azure Active Directory. 
*  Your account must have Microsoft.Authorization/*/Write access to assign an active directory application to a role.

These permissions can be configured through the Azure Portal, CLI tool, or PowerShell. A guide to setting up the Azure service principals in the Azure portal is provided in the [Azure Service Principal](azure-sp.md) section.


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
    	"subscriptionId": "6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1",
		"tenantId":       "11f54b31-ffb9-42b5-8fda-76c734a7796c",
		"clientId":       "4d95b358-079d-4d6d-85c4-943c0f1d91cd",
		"clientSecret":   "tMQ5ZEP?.sj46e15123ba3b5b]"
}
```
Create the base Secret via the CLI substituting a path of your choosing:

```BASH
thy secret create --path azure/base/api-account --data '@secret_base.json' --attributes '{"type": "azure"}' --desc "azure base credential"
```

## Dynamic Secrets
In DSV you can create dynamic Secrets from either an existing service principal or create a temporary service principal. 

>NOTE Temporary vs Existing Service Principals:  Azure does not use these terms, but DSV can either use a service principal that you have already setup (existing)  or DSV can create a service principal on the fly (temporary) through Azure's role-based access control (RBAC).

>If possible, a temporary service principal is preferred. Temporary service principals are independent from other service principals and provide fine grained access and auditing. However, creating temporary service principals can take up to 2 minutes before fully provisioned on Azure. 

>Use of an existing service principal is required in some cases when Azure services are not accessible through Azure RBAC. In these cases, an existing service principal can be set up with the necessary access and DSV can create a new client secret for this service principal each time the dynamic secret is read.  One issue with this might be that Azure limits the number of passwords for a given Application object, but this can be managed by reducing the secret TTL. Also keep in-mind that Azure does not log actions related to each secret, so auditing is not a clean as with temporary service principals. 


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
	"appId": "f81b3c6d-2ce9-47d4-ad2d-fef8390792a2", 
	"appObjectId" : "5fe218ee-cb58-4089-ac9f-b1b68971ad73",
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
        "appId": "f81b3c6d-2ce9-47d4-ad2d-fef8390792a2",
        "appObjectId": "5fe218ee-cb58-4089-ac9f-b1b68971ad73",
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "azure:base:api-account"
        },
        "roleName": "Contributor",
        "ttl": 360
    },
    "data": {
        "appObjectId": "5fe218ee-cb58-4089-ac9f-b1b68971ad73",
        "client_id": "f81b3c6d-2ce9-47d4-ad2d-fef8390792a2",
        "client_secret": "bfe6ac86-3671-4fd9-8f76-8f2e0f22495d",
        "role": "Contributor",
        "subscription_id": "6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1",
        "tenant_id": "11f54b31-ffb9-42b5-8fda-76c734a7796c",
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
	"scope":          "/subscriptions/<Azure Subscription ID>/resourceGroups/<resource group name>",
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
        "roleId": "/subscriptions/6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "roleName": "Contributor",
        "scope": "/subscriptions/6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1/resourceGroups/dsv-resource-group",
        "ttl": 36000
    },
    "description": "azure root credential",
    "data": {
        "appObjectId": "e463477c-7d90-4743-92f2-c7f44ede8ec9",
        "client_id": "945d25cb-7697-4648-b574-e8a660154269",
        "client_secret": "ce1d072d-449d-4052-9a81-0d7ef982f7a4",
        "role": "Contributor",
        "roleAssignmentId": "/subscriptions/6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "roleAssignmentStatus": "created",
        "roleAssignmentTaskId": "task_3da0a37c-0a1c-4ebd-8829-dbe7b988b36f",
        "spObjectId": "1782611c-99c2-418b-b672-783e3cf8bd14",
        "subscription_id": "6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1",
        "tenant_id": "11f54b31-ffb9-42b5-8fda-76c734a7796c",
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
| GET                 | /v1/task/status/{roleAssignmentTaskId}      |

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