[title]: # (Architecture)
[tags]: # (,)
[priority]: # (1110)

# Architecture

DevOps Secrets Vault unites two main components:

* on the customer premises: locally installed, OS-specific CLI executables on workstations used to operate DevOps Secrets Vault

* in the cloud: a secrets vault created as a tenant per DSV as a multi-tenancy SaaS delivered by an AWS instance

Architectural Summary View: DevOps Secrets Vault
![Image](./images/dsv-architecture-simple-01.png)

* DSV supports the AWS and Azure cloud authentication providers.

* Activities originate at the customer premises one of three ways:

  * a command entered manually at the CLI
  
  * a command issued by a running shell script or application
  
  * an API call by an application
  
* The API Gateway receives API calls, obtains the responses, and relays them to the caller, using HTTP GET, PUT, POST and other methods common to the REST architectural style.

* The Authorizer uses OAuth to handle API Gateway authorization.

* The Vault Application hosts the core DSV application, essentially a set of AWS Lambda (serverless) commands. Lambda auto-scales to demand.

* Extensive logging enables strong audit trails and protections, while encryption protects secrets at rest in the vault.
