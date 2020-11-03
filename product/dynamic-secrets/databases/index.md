[title]: # (Database Dynamic Secrets)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6400)

# Database Dynamic Secrets

Both Database Dynamic Secrets and IaaS Dynamic Secrets provide temporary credentials for very specific uses. The possible damage done by leaked credentials is severely limited to due to granular policies and short time-to-live. However, IaaS platforms provide mechanisms for temporary credentials with fine-grained policies, and most databases do not. Therefore, DSV provides a way to provide temporary credentials by creating and deleting users in a just-in-time manner.

![architecture](../images/mysql.png "Database Architecture")

## DSV Engine Required
Database Dynamic Secrets require the deployment of the DSV Engine.  See the instructions at [DSV Engine](../../engine/index.md).
