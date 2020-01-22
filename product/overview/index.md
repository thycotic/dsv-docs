[title]: # (Overview)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1100)

# Overview

DevOps Secrets Vault (DSV) offers a cross-platform, up to date solution for securing your organization’s Secrets (most commonly, credentials) in a Cloud-hosted Vault, where your staff administer them using a Command Line Interface (CLI) and your applications access them programmatically by calls to a RESTful Application Programming Interface (API).

DSV is a back-office and developer tool. It meets your technical staff where they live—at the command line—and talks to your applications in the language they speak, through an API.

DevOps Secrets Vault allows faster automation of Secrets operations at higher volumes, suiting it for use by non-human actors, such as servers (physical or virtual), containers, and applications. DSV helps eliminate risky practices like hardcoding credentials in configuration files, or storing them in an Excel spreadsheet.

## Application Model

Thycotic created DevOps Secrets Vault as a modern application rooted in the serverless architecture of Amazon Web Services (AWS) Lambda. In congruence with the Software-as-a-Service (SaaS) model, the Secrets Vaults and the API reside in the Cloud, and Thycotic provisions each customer with a tenancy.

However, the CLI (Command Line Interface) must be considered core to the product as well, and this installs locally. The Thycotic website offers CLI executable downloads for Windows, Linux, and macOS in both 32 and 64-bit architectures.

* Recognizing the value of transparency for security products, Thycotic offers the CLI for DevOps Secrets Vault as an **open source, customizable component** of the DSV Cloud service, with the source code available on Github.

See [Obtain DSV](../obtain/index.md) for instructions on how to get DevOps Secrets Vault up and running for your organization.

## Features

DevOps Secrets Vault characteristics include:

* efficient Command Line Interface (CLI)
* automation-oriented, RESTful API uses HTTP requests to GET, PUT, POST and otherwise engage data
  * familiar territory for developers, providing an easy learning curve, faster development cycles, and more supportable solutions
* API built using AWS Lambda serverless architecture, ensuring strong security, high availability, and automatic unlimited scaling for capacity
* designed for speed and agility with microservices
* local caching options handle high performance workloads with reduced latency and fewer API calls
* authentication by passwords, client Secret keys, and federation through ThycoticOne, AWS IAM, and Azure MSI (Google Cloud planned)
  * Users and Roles of the federated services can be used to provide federated authentication to the DevOps Secrets Vault; by assigning Roles as they are created, this solves the bootstrapping issue of autoscaling services
* JWT-formatted, OAuth-compliant access tokens
* SOC 2 Type II conformant—affirmative audit results by independent organization available

## DevOps Secrets Vault Extensions

DSV extensions support its use with other tools, allowing them to authenticate to DevOps Secrets Vault and thereby enabling a centralized vault for all DevOps needs. Currently, Thycotic offers two extensions.

* The [Jenkins](/dsv-extension-jenkins) extension supports shops using Jenkins for continuous integration.
* The [Kubernetes](/dsv-extension-kubernetes) extension supports organizations using Kubernetes to manage containerized applications.

Extensions expected in future DSV releases include Puppet, OpenShift, Chef, Ansible, and Salt, among others.

## Java SDK for DevOps Secrets Vault

For customers interested in creating their own extensions to integrate DSV into their infrastructure, Thycotic provides the [DSV Java SDK](/dsv-sdk-java).

## DevOps Secrets Vault API Documentation

For those doing application development, the separately located [API Documentation](https://dsv.thycotic.com/api) lists the available API calls and details their correct use for quick integration and customization into your applications.

## Tools for Development

As detailed by [Obtain DSV](../obtain/index.md), Thycotic offers a feature-complete, non-time-limited **free version** of DevOps Secrets Vault that supports up to 250 Secrets and 2500 API calls a month.

Thycotic also offers customers extra **Sandbox** tenancies for testing configurations and functionality before production deployment.

![](./images/spacer.png)

![](./images/spacer.png)
