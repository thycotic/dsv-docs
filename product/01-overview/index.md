[title]: # (Overview)
[tags]: # (,)
[priority]: # (1100)

# Overview

DevOps Secrets Vault (DSV) offers a cross-platform, cloud-oriented solution for secure application access to your organization’s credentials.

* DSV uses Amazon Web Services to host its secrets vaults in the cloud, ensuring strong security, high availability, and automatic scaling for capacity.

* Although the vaults reside in the cloud, DSV (being a command line application) must be installed locally on each workstation where it will be used. Thycotic offers DSV executables for Windows, Linux, and the MacOS.

Because it provides both a Command Line Interface (CLI) for human operators and an Application Programming Interface (API) for programmatic operators, DSV makes credentials readily available even as they remain highly secure. With DSV optimized for automation and SOC2 compliant, no excuse remains for risky practices like hardcoding credentials in configuration files, or storing them in an Excel spreadsheet. 

Besides credentials, DSV accepts almost any file as a secret, for example, sensitive legal documents or spreadsheets. This creates almost endless use cases, limited only by your creativity. In line with that potential, DSV

* supports both AWS and Azure cloud authentication

* features local caching for high performance under load

* offers extensions to work with Jenkins and Kubernetes, with more to come

* includes a Java SDK 

