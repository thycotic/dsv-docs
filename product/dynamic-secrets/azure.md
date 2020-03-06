[title]: # (Azure Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6100)

# Azure Dynamic Secrets

The Azure secrets engine dynamically generates Azure service principals along with role assignments.

Each service principal is associated with a TTL. When the TTL expires, the service principal will be deleted.

If an existing service principal is specified as part of the dynamic secret role, a new client secret will be dynamically generated instead of a new service principal. The client secret will be deleted when the ttl is expired.

These are the links to Azure documentation on service principal:

* [Service principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)
* [howto-create-service-principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)


## Azure Service principal 

#### Authentication

DSV must have sufficient permissions to read Azure role information and manage service principals. The individual parameters are described in the section below.

The following Azure roles and Azure Active Directory permissions are required, regardless of which authentication method is used:

"Owner" role for the subscription scope  
"Read and write all applications" permission in azure active directory 

These permissions can be configured through the Azure Portal, CLI tool, or PowerShell. In your Azure subscription, your account must have Microsoft.Authorization/*/Write access to assign an AD app to a role. This action is granted through the Owner role or User Access Administrator role. If your account is assigned to the Contributor role, you don't have adequate permission. You will receive an error when attempting to assign the service principal to a role.



#### Create the Base Secret

Creates the credentials required for the DSV to perform API calls to Azure. These credentials will be used to query roles and create/delete service principals. 

`subscription_id` (string: <required>) - The subscription id for the Azure Active Directory.  
`tenant_id` (string: <required>) - The tenant id for the Azure Active Directory.   
`client_id` (string:"") - The OAuth2 client id to connect to Azure.   
`client_secret` (string:"") - The OAuth2 client secret to connect to Azure.  
`environment` (string:"") - The Azure environment. If not specified, DSV will use Azure Public Cloud.



| method                 | path                                                         |
| --------------         | ------------------------------                               | 
| POST                 | /v1/secrets/azure/{secretpath}                                 |                                                      /


```json
{
    "data": {
    	"subscriptionId": "028b0a8e-84d2-4797-9071-456fe718248e",
		"tenantId":       "s0kkfsld-569l-491f-err4-7f4d831987fd",
		"clientId":       "f0b2cbb9-5782-42d5-8647-028b0a8e61f2",
		"clientSecret":   "tMQ5ZEP?.sjfk5435jlKJSDedDKJDKJD@]"},
	
    "description": "azure root credential",
    "attributes": {
		 "type": "azure"
    }
}
```
#### Roles
In DSV you can create service principal either from existing application object or dynamic service principal from Azure role with role-specific TTL. If an existing service principal is not provided, DSV configured Azure role will be assigned to a newly created service principal. 


#### Create the Dynamic Secret 
Create a Dynamic Secret, which points to the base Secret via its attributes. The Dynamic Secret doesn't have any data stored in it because data is only populated when you read the Secret.

| method                 | path                                                         |
| --------------         | ------------------------------                               | 
| POST                 | /v1/secrets/dynamic/azure/{secretpath}       

| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| roleName                  | azure role name to be assigned to the generated service principal |
| roleId                    | azure role id to be assigned to the generated service principal                |
| scope                     | optional azure role name to be assigned to the generated service principal
| appId                     | optional application  ID for an existing service principal that will be used instead of creating dynamic service principals
| appObjectId               | optional application Object ID for an existing service principal that will be used instead of creating dynamic service principals.
| ttl                       | optional time to live in seconds of the generated token. If none is specified it will default to 900 |

For CLI:  
Create an attributes json file named `secret_attributes.json' substituting your values.


#####  Static Service Principal 
The `Application Object ID` must be set on the DSV if an existing service principal is to be used.

```json
{
  
	
    "description": "azure root credtial",
    "attributes": {
		"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "azure:base:api-account"
	},
	"roleName":       "Contributor",
	"appId": "08cfa61b-56ec-46c7-9780-247cfac0ef0c", 
	"appObjectId" : "69297292-8373-4e4b-8679-434439b57347",
	"ttl": 360
	
    }
}
```

CLI:  
```BASH
thy secret create --path dynamic/azure/{secretpath} --attributes @secret_attributes.json
```

Now anytime you read the Dynamic Secret, the data is populated with the temporary azure access credentials.


```BASH
thy secret read --path dynamic/azure/{secretpath}
```

returns a result like:


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

#### Dynamic Service Principal

If dynamic service principals are used, Azure roles must be configured on DSV. Azure roles may be specified using the role_name  (example "Reader"), or role_id ("/subscriptions/.../roleDefinitions/..."). 

Note:  Because creating service principal and assigning role in same request takes 10s of seconds (over a minute has been seen), The calls have been broken down into two separate calls. In the first call the service principal will be returned along with the task id that fired in the background for role assignment. You can check the role assignment task with another api call.

| role Attribute          | description                                                         |
| --------------         | ------------------------------                               | 
| roleName                  | azure role name to be assigned to the generated service principal      
| roleId                    |  azure role id to be assigned to the generated service principal
| scope                     |  azure role name to be assigned to the generated service principal

```json
{
    "attributes": {
		 "linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "azure:base:api-account"
	},
	"roleName":       "Contributor",
	"roleId":         "/subscriptions/5f74ce1f-84d2-4797-9071-456fe718248e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
	"scope":          "/subscriptions/5f74ce1f-84d2-4797-9071-456fe718248e/resourceGroups/dsv-secret-test",
	"ttl": 36000
    }
}

```

Now anytime you read the Dynamic Secret, the data is populated with the temporary azure access credentials.

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

Check the role assignment task status (use the `roleAssignmentTaskId` from above response)

| method                 | path                                                         |
| --------------         | ------------------------------                               | 
| GET                 | /v1/task/status/{roleAssignmentId}      

```json
{
    "taskName": "azure_role_assignment",
    "state": "SUCCESS",
    "results": null,
    "error": "",
    "createdAt": "2020-03-04T19:28:07.420285103Z"
}
```


### Choosing between dynamic or existing service principals

If the Azure resources can be provided via the RBAC system and Azure roles defined in DSV  then  Dynamic service principal is preferred . This form of credential is completely decoupled from any other clients and gives audit granularity.

However, Creating Dynamic service principals takes significant  amount of seconds(10s of seconds are common, and over a minute has been seen ) and also access to some Azure services cannot be provided with the RBAC system, . In these cases, an existing service principal can be set up with the necessary access, and DSV can create new client secret for this service principal. But Any changes to the service principal permissions affect all clients and Azure does not provide any logging with regard to which credential was used for an operation.

An important limitation when using an existing service principal is that Azure limits the number of passwords for a single Application. An error will be returned if the object size is reached. This limit can be managed by reducing the role TTL.




![](./images/spacer.png)

![](./images/spacer.png)