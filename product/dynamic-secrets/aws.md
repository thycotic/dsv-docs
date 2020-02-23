[title]: # (AWS, Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6100)

# AWS Dynamic Secrets

AWS Dynamic Secrets generate a temporary access key, secret key, and session token. AWS security token service (STS) for provides either `federate` or `assumeRole`.  `federate` is ideal for assigning dynamic secrets from a single AWS account.  `assumeRole` allows cross account access in AWS, so a single set of credentials in DSV can grant access to multiple AWS accounts.

These are the links to AWS documentation for each STS type:

* [Federate](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)
* [Assume Role](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)

## AWS Federate

### Setup the AWS IAM User
For the `federate` example, create a new IAM User and note the access key and secret key.

Assign a policy to the IAM user with `sts:GetFederationToken` permission as well as any other permissions the IAM user should have.  In this example, we assign the user full CodeDeploy rights.

> NOTE: When you get temporary tokens from AWS via `GetFederationToken` the resulting token's permissions will be the intersection of the IAM User and the policy ARN specified on the Dynamic Secret. In other words, the Dynamic Secret is only allowed the permissions that are in both the IAM policies and the Dynamic secret attached policy.


```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sts:GetFederationToken",
                "codedeploy:*"
            ],
            "Resource": "*"
        }
    ]
}
```




### Create the Base Secret

Next create a Secret in DSV with the AWS IAM user access key, secret key, and region.

Create a file named `secret_root.json` substituting your values:

```json
{
        "accessKey": "AIA2RAVTSMNW437LM",
        "region": "us-east-1",
        "secretKey": "SpN5Ipjvgepz0/q0ZNGmFhhLkUr+Uie5+D3CE"
}

```

Create the Secret via the CLI at a path of your choosing:

```BASH
thy secret create --path aws/base/api-account --data @secret_root.json --attributes '{"type": "aws"}'
```

### Create the Dynamic Secret

| Attribute                 | Description                                                         |
| --------------            | ------------------------------                                      |
| policyArn                 | AWS ARN of the policy to assign the federated user token. Can be customer or aws managed |
| providerType              |  `federate`                       |
| ttl                       | optional time to live in seconds of the generated token. If none is specified it will default to 900 |


Now you need to create a Dynamic Secret, which points to the base Secret via its attributes. The Dynamic Secret doesn't have any data stored in it because data is only populated when you read the Secret.

Create an attributes json file named `secret_attributes.json' substituting your values.


```json
{
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "aws:base:api-account"
	},
	"policyArn": "arn:aws:iam::aws:policy/AWSCodeDeployReadOnlyAccess",
	"providerType": "federate",
	"ttl": 1200
}
```

Create a new Dynamic Secret

```BASH
thy secret create --path dynamic/aws/federate-api --attributes @secret_attributes.json
```

Now anytime you read the Dynamic Secret, the data is populated with the temporary AWS access credentials.


```BASH
thy secret read --path dynamic/aws/federate-api
```

returns a result like:


```json
{
  "attributes": {
    "linkConfig": {
      "linkType": "dynamic",
      "linkedSecret": "aws:base:api-account"
    },
    "policyArn": "arn:aws:iam::aws:policy/AWSCodeDeployReadOnlyAccess",
    "providerType": "federate",
    "ttl": 1200
  },
  "data": {
    "accessKey": "ASIAZTRAVTSMN5P6P",
    "expiration": "2020-02-06T18:49:17Z",
    "secretKey": "Is5L79Y1LgtOistJv+x0yVZ2/KLPWUUsUUj",
    "sessionToken": "FwIv...Zggfj+6nbiT9IOrEw==",
    "ttl": 1200
  },
  "description": "",
  "id": "db38e569-5d7f-4ad8-954c-ac846d528947",
  "version": "0"
}
```

You can validate the credentials only grant read access to Code Deploy by putting the credentials in a python script and attempting to create a Code Deploy Application:

```python
import boto3
import json
from botocore.exceptions import ClientError

sess = boto3.Session(
    aws_access_key_id="ASIAZTRAVTSMN5P6P",
    aws_secret_access_key="Is5L79Y1LgtOistJv+x0yVZ2/KLPWUUsUUj",
    aws_session_token="FwIv...Ay93XTqVBGyeuodcw=="
)

client = sess.client("codedeploy")
resp = client.list_applications()
print("----list code deploy apps----")
print(json.dumps(resp["applications"], indent=4))

print("----create code deploy app----")
try:
    resp = client.create_application(
        applicationName="TestApp",
        computePlatform="Server"
    )
except ClientError as e:
    print(e.response["Error"]["Code"])
```

The result should look something like this (depending on how many CodeDeploy apps exist)

```BASH
----list code deploy apps----
[
    "ExampleApp"
]
----create code deploy app----
AccessDeniedException
```

![](./images/spacer.png)



## AWS Assume Role

In this example, we assume the IAM user and the role that that user will assume are in separate AWS accounts.  This is not required, but then it might make more sense to use the `sts:Federated`approach.

### Setup the AWS IAM user
In the AWS account for the IAM user, create or modify an IAM user policy to include the `sts:AssumeRole` permissions as well as any other permissions the user should have.  In this example, we assign the user full `CodeDeploy` rights.

>NOTE: For setting up, if you don't know the role account ID or name at this point, **Resources** could be set to all `*`, but best practices would be to come back and restrict the **Resources** to only the role once the name is known as shown here.


```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codedeploy:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "sts:AssumeRole"
            ],
            "Resource": "arn:aws:iam::{account id of role}:role/{role-name}""
        }
    ]
}
```
### Setup the AWS IAM role
In the AWS account with the role that is to be used, [create a new Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) or identify an existing one with the proper policies (not shown here). 
>NOTE: The `sts:AssumeRole` token will have permissions that intersect between the IAM user policy(ies) and the role ploicy(ies) they assume.  In other words, the token can't have permissions enabled by both the user and role policies. 

Additionally, this role must have a trust relationship setup between the IAM user in the first account and this role.  It might look like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::{account id of user}:{iam-user}"
      },
      "Action": "sts:AssumeRole",
      "Condition": {}
    }
  ]
}
```
### Create the Base Secret

Next create a Secret in DSV with the AWS IAM user access key, secret key, and region.

Create a file named `secret_root.json` substituting your values:

```json
{
        "accessKey": "AIA2RAVTSMNW437LM",
        "region": "us-east-1",
        "secretKey": "SpN5Ipjvgepz0/q0ZNGmFhhLkUr+Uie5+D3CE"
}
```
Create the Secret via the CLI at a path of your choosing:

```BASH
thy secret create --path aws/base/api-account --data @secret_root.json --attributes '{"type": "aws"}'
```

### Create the Dynamic Secret


| Attribute                 | Description                                                                                       |
| --------------            | ------------------------------                                                                    |
| roleArn                 | AWS ARN of the role to assign the AssumeRole user token. Can be customer or aws managed |
| providerType              | `assumeRole`                                                      |
| ttl                       | optional time to live in seconds of the generated token. If none is specified will default to 900 |


### Create the Dynamic Secret

Now you need to create a Dynamic Secret, which points to the base secret via its attributes. The Dynamic Secret doesn't have any data stored in it. Data is only populated when you read the secret.

Create or update the attributes json file named `secret_attributes.json substituting the ARN of the role you created.


```json
{
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "aws:base:api-account"
	},
	"roleArn": "arn:aws:iam::{account id of role}:role/{role-name}",
	"providerType": "assumeRole",
	"ttl": 1200
}
```
Now create the dynamic secret in the CLI using the josn above.
```BASH
thy secret create --path dynamic/aws/assume-api --attributes @secret_attributes.json
```

Now anytime you read the Dynamic Secret, the data is populated with the temporary AWS access credentials.


```BASH
thy secret read --path dynamic/aws/assume-api
```

returns a result like:


```json
{
  "attributes": {
    "linkConfig": {
      "linkType": "dynamic",
      "linkedSecret": "aws:base:api-account"
    },
    "roleArn":  "arn:aws:iam::{account id of role}:role/{role-name}",
    "providerType": "assumeRole",
    "ttl": 1200
  },
  "data": {
    "accessKey": "ASIAZTRAVBIVK5SLU",
    "expiration": "2020-02-06T18:49:17Z",
    "secretKey": "Xh/xqw5Ipjvgepz0i6un+ZUUsUUj",
    "sessionToken": "FwIv...Zggfj+TexEiLtE3h1R1UvllXCHzk5==",
    "ttl": 1200
  },
  "description": "",
  "id": "34fb64d7-18da-453d-9487-3d1c082ba372",
  "version": "0"
}
```


![](./images/spacer.png)

![](./images/spacer.png)