[title]: # (AWS, Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1910)

# AWS Dynamic Secrets

AWS Dynamic Secrets generate a temporary access key, secret key, and session token either through user federation or assuming a role. There are two options through the AWS STS service for providing temporary credentials. 

* [Federate](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)
* [Assume Role](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)

## AWS Attribute Options

Dynamic Secrets link to the base credentials via the attribute metadata. Options for AWS are as follows:


| Attribute                 | Description                                                                                       |
| --------------            | ------------------------------                                                                    |
| policyArn                 | AWS ARN of the policy to assign the federated user token. Can be customer or aws managed                      |
| providerType              | valid values are: `federate` or `assumeRole`                                                      |
| ttl                       | optional time to live in seconds of the generated token. If none is specified will default to 900 |


## AWS Setup

### IAM User
For the below examples create a new IAM User and note the access key and secret key. 

Assign a policy to the IAM use with `sts:GetFederationToken` and `sts:AssumeRole` permissions as well as any other permissions the Dynamic Secret credentials should have.

> NOTE: When you get temporary tokens from AWS via `AssumeRole` or `GetFederationToken` the resulting token's permissions will be the intersection of the IAM User and either the policy ARN or role ARN specified on the Dynamic Secret. So a Dynamic Secret cannot have greater permissions than the IAM User has.

In this example we will grant full access to the Code Deploy service, which will be scoped down for the federated user.

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

### IAM Role

In the AWS IAM console create a new Role for the IAM User to assume. The Role must have a trust relationship that allows the user to assume it. Substitute your AWS account id and IAM username in the below statement:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::{accountid}:{iam-user}"
      },
      "Action": "sts:AssumeRole",
      "Condition": {}
    }
  ]
}
```

```json
Once the Role is created you can add the `sts:AssumeRole` permission to the User's policy so it looks like:

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
        },
        {
            "Effect": "Allow",
            "Action": [
                "sts:AssumeRole"
            ],
            "Resource": "arn:aws:iam::{accountid}:role/{role-name}""
        }
    ]
}
```


### Create the Base Secret

Next create a Secret in DSV with the access key, secret key, and region. 

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

## Federation Token


### Create the Dynamic Secret

Now you need to create a Dynamic Secret, which points to the base Secret via its attributes. The Dynamic Secret doesn't have any data stored in it, data is only populated when you read the Secret.

Create an attributes json file named `secret_attributes.json substituting your values.


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

The result should look like

```BASH
----list code deploy apps----
[
    "ExampleApp"
]
----create code deploy app----
AccessDeniedException
```

![](./images/spacer.png)



## Assume Role

The primary reason to use `assumeRole` rather than `federate` is that it allows cross account access in AWS, so a single set of credentials in DSV can grant access to multiple AWS accounts.



### Create the Dynamic Secret

Now you need to create a Dynamic Secret, which points to the base secret via its attributes. The Dynamic Secret doesn't have any data stored in it, data is only populated when you read the secret.

Create or update the attributes json file named `secret_attributes.json substituting the ARN of the role you created.


```json
{
	"linkConfig": {
		"linkType": "dynamic",
		"linkedSecret": "aws:base:api-account"
	},
	"roleArn": "arn:aws:iam::{accountid}:role/{role-name}",
	"providerType": "assumeRole",
	"ttl": 1200
}
```

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
    "roleARn": "arn:aws:iam::aws:policy/AWSCodeDeployReadOnlyAccess",
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



