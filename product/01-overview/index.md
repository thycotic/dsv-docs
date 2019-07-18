[title]: # (Overview)
[tags]: # (,)
[priority]: # (1100)

# Overview

DevOps Secrets Vault (DSV) offers a cross-platform, up to date solution for securing your organization’s secrets (most commonly, credentials) in a cloud-hosted vault, where your staff administer them using a Command Line Interface (CLI) and your applications access them programmatically by calls to a RESTful Application Programming Interface (API).

DSV marks an expansion of Thycotic’s product line to include back-office and developer tools. It meets your technical staff where they live—at the command line—and talks to your applications in the language they speak, through an API. DevOps Secrets Vault allows faster automation of secrets operations at higher volumes, suiting it for use by non-human actors, such as servers (physical or virtual), containers, and applications. DSV helps eliminate risky practices like hardcoding credentials in configuration files, or storing them in an Excel spreadsheet.

## Application Model

Thycotic created DevOps Secrets Vault as a modern application rooted in the serverless architecture of Amazon Web Services (AWS) Lambda. In congruence with the Software-as-a-Service (SaaS) model, the secrets vaults and the API reside in the Cloud, and Thycotic provisions each customer with a tenancy.

However, the CLI (Command Line Interface) must be considered core to the product as well, and this installs locally. The Thycotic website offers CLI executable downloads for Windows, Linux, and MacOS in both 32 and 64-bit architectures. Importantly, Thycotic offers the CLI for DevOps Secrets Server as an open source, customizable component of the DSV cloud service, with the source code available on Github under the (which?) open source license.

* See [Obtain DSV](../02-obtain/index.md) for instructions on downloading and installing the executables and obtaining the source code.

## Features

DevOps Secrets Vault characteristics include:

* efficient Command Line Interface (CLI)
* automation-oriented, RESTful API uses HTTP requests to GET, PUT, POST and otherwise engage data
  * familiar territory for developers, for an easy learning curve, faster development cycles, and more supportable solutions
* API built using AWS Lambda serverless architecture, ensuring strong security, high availability, and automatic unlimited scaling for capacity
* designed for speed and agility with microservices
* local caching options handle high performance workloads with reduced latency and fewer API calls
* authentication by passwords, client secret keys, and federation through ThycoticOne, AWS IAM, and Azure MSI (Google Cloud planned)
  * users and roles of the federated services can be used to provide federated authentication to the DevOps Secrets Vault; by assigning roles as they are created, this solves the bootstrapping issue of autoscaling services
* JWT-formatted, OAuth-compliant access tokens
* SOC 2 Type II conformant—affirmative audit results by independent organization available

### Extensible with Plug-ins

DevOps Secrets Vault supports multiple programming languages and DevOps tools via extensions and SDKs.

Currently, Thycotic provides downloadable plug-ins for [Jenkins](../extensions/jenkins/index.md) and [Kubernetes](../extensions/kubernetes/index.md) to authenticate to DevOps Secrets Vault and enable a centralized vault for all DevOps needs. Like the CLI, Thycotic provides these as open-source tools with the code available on GitHub.

For quick integration and customization into customer applications, Thycotic provides a downloadable [Java SDK](../sdk/java/index.md).

### Tools for Development

As detailed by [Obtain DSV](../02-obtain/index.md), Thycotic offers **Sandbox** tenants for testing configurations and functionality before production deployment. Thycotic also offers a feature-complete, non-time-limited **free version** of DevOps Secrets Vault that supports up to 250 secrets and 2500 API calls a month.
