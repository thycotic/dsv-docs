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
| subscriptionId              | Required - The subscription ID holding the resources you wish to access using Azure Active Directory.   |
| tenantId                    | Required - The tenant ID for Azure Active Directory. Azure lists it in places as "Directory (tenant) ID"                  |
| clientId                    |  Required - The OAuth2 client ID to connect to Azure. Azure lists it in places as "Application (client) ID" |
| clientSecret                |  Required - The OAuth2 client secret to connect to Azure.
| environment                 |  Optional - The Azure environment. If not specified, DSV will use Azure Public Cloud. |

![](./images/spacer.png)

Create a file named `secret_base.json` substituting your values:

```json
{
    	"subscriptionId": "yourusbscriptionId",
		"tenantId":       "yourtenantId",
		"clientId":       "yourclientId",
		"clientSecret":   "yoursecret"
}
```
Create the base Secret via the CLI substituting a path of your choosing:

```BASH
dsv secret create --path azure/base/api-account --data '@secret_base.json' --attributes '{"type": "azure"}' --desc "azure base credential"
```

## Dynamic Secrets
In DSV you can create dynamic Secrets from either an existing service principal or create a temporary service principal. 

>NOTE Temporary vs Existing Service Principals:  Azure does not use these terms, but DSV can either use a service principal that you have already setup (existing)  or DSV can create a service principal on the fly (temporary) through Azure's role-based access control (RBAC).

>If possible, a temporary service principal is preferred. Temporary service principals are independent from other service principals and provide fine grained access and auditing. However, creating temporary service principals can take up to 2 minutes before fully provisioned on Azure. 

>Use of an existing service principal is required in some cases when Azure services are not accessible through Azure RBAC. In these cases, an existing service principal can be set up with the necessary access and DSV can create a new client secret for this service principal each time the dynamic secret is read.  One issue with this might be that Azure limits the number of passwords for a given Application object, but this can be managed by reducing the secret TTL. Also keep in-mind that Azure does not log actions related to each secret, so auditing is not a clean as with temporary service principals. 


## Dynamic Secret for an Existing Service Principal
Create a dynamic Secret that points to the base Secret via its attributes. **The dynamic Secret does not have any data stored in it because data is only populated when you read the Secret.**


| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| roleName                  | Optional- Azure role name to be assigned to the existing service principal.  Does not change existing principal's role    |
| appId                     | Required - Application (client) ID for an existing service principal                   |
| appObjectId               | Required - Application Object ID for an existing service principal             |
| ttl                       | Optional - Time to live in seconds of the generated token. If none is specified it will default to 900 |

![](./images/spacer.png)

1. Create an attributes json file named `secret_attributes.json` substituting your values

    ```json
    {
    	"linkConfig": {
    		"linkType": "dynamic",
    		"linkedSecret": "azure:base:api-account"
    	},
    	"roleName":       "Contributor",
    	"appId": "f81b3c6d-2ce9-47d4-ad2d-fef8390792a2", 
    	"appObjectId" : "5fe218ee-cb58-4089-ac9f-b1b68971ad73",
    	"ttl": 900  
    }
    ```
1. Create the dynamic Secret via the CLI substituting the path of your choosing.
 
    ```BASH
    dsv secret create --path azure/dynamic/api-account --attributes '@secret_attributes.json' --desc "azure dynamic credential"
    ```
1. Now anytime you read the dynamic Secret, the data is populated with the temporary Azure access credentials. The input
    ```BASH
    dsv secret read --path azure/dynamic/api-account
    ```
Will return the result:

```json
{
    "id": "yourId",
    "path": "dynamic:azure:sp-static",
    "attributes": {
        "clientId": "yourpaddId",
        "appObjectId": "yourappObjectId",
        "linkConfig": {
            "linkType": "dynamic",
            "linkedSecret": "azure:base:api-account"
        },
        "roleName": "Contributor",
        "ttl": 900
    },
    "data": {
        "appObjectId": "yourappObjectId",
        "clientId": "yourclientId",
        "clientsecret": "yoursecret",
        "role": "Contributor",
        "subscriptionId": "yoursubscriptionId",
        "tenantId": "yourtenantId",
        "ttl": 900
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
| roleName                  | Optional - If no "roleID" is assigned, DSV will try to look-up the built-in Azure role by this name.      
| roleId                    | Optional - Azure role id to be assigned to the temporary service principal.  If not defined, then DSV will attempt to look up the Azure built-in role by "roleName".   However, role ID takes precedence.  One of roleName or roleID required.
| scope                     | Required - Azure resource group to be assigned to the temporary service principal
| ttl                       | Optional - Time to live in seconds of the generated token. If none is specified it will default to 900. |

>Note:  Azure built-in role names and IDs can be found [here](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)

1. Create an attributes json file named `secret_attributes.json` substituting your values.

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
2. Create a new Dynamic Secret via the CLI substituting the path of your choosing.
 
```BASH
dsv secret create --path /azure/dynamic/api-account --attributes '@secret_attributes.json' --desc "azure dynamic credential" 
```
3. Now anytime you read the dynamic Secret, the data is populated with the temporary azure access credentials.

```json
{
    "id": "yourId",
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
        "clientId": "945d25cb-7697-4648-b574-e8a660154269",
        "clientSecret": "yoursecret",
        "roleName": "Contributor",
        "roleId": "youroleId",
        "roleAssignmentStatus": "created",
        "roleAssignmentTaskId": "task_3da0a37c-0a1c-4ebd-8829-dbe7b988b36f",
        "servicePrincipleId": "1782611c-99c2-418b-b672-783e3cf8bd14",
        "subscriptionId": "6ca2adeb-7b44-4c7f-93fc-2d5b9729a8c1",
        "tenantId": "11f54b31-ffb9-42b5-8fda-76c734a7796c",
        "ttl": 36000
    },
    "created": "2020-02-12T20:57:44Z",
    "lastModified": "2020-03-04T19:27:45Z",
    "version": "12"
}
```

4. It takes some time for the temporary service principal to be created, so you can check using the Azure portal for the new service principal or use the DSV API: 
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