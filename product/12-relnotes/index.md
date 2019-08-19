[title]: # (Release Notes)
[tags]: # (,)
[priority]: # (2200)

# Release Notes

The July 2019 release of DevOps Secrets Vault, corresponding to version 1.0.0, marked the product’s first general availability release.

Thycotic offers DevOps Secrets Vault as a cloud service, so all DSV users always receive the currently offered service.

* However, users operate DSV through a Command Line Interface (CLI) provided by downloaded, locally installed, and OS-specific executables.
* Thycotic periodically updates these OS-specific executables to deliver fixes, improvements, and feature additions.
* Generally, older versions of the CLI executable for an OS will continue to work, but you will want to have the latest to benefit from fixes and obtain new features.

This article includes a series of tables to track changes to DSV. The first table covers the cloud basis for DSV, and the others cover the OS-specific CLI executables. 
  
---
  
DSV Cloud Service: Version History (Most Recent First)

| **Version** | **Date**   | **Notes**                          |
|-------------|------------|------------------------------------|
| 1.0.0       | 2019.07.23 | first General Availability release |
  
---
  
DSV CLI Executable for Windows: Version History (Most Recent First)

| **Version** | **Date**   | **Notes**                                                                                                                                      |
|-------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.0.1       | 2019.08.15 | fixed issue where the CLI returned an error when the stored refresh token had been invalidated and the current cached access token was expired |
| 1.0.0       | 2019.07.23 | first General Availability release                                                                                                             |

-   when you upload a config document from a file via the **config update**
    command, the updated config from the cloud is now saved back to the local
    file; this ensures the return to the user of any auto-generated IDs for the
    permission policies and settings

-   fixed initialization issues with windows credential manager and linux pass
    storage
  
---
  
DSV CLI Executable for Linux: Version History (Most Recent First)

| **Version** | **Date**   | **Notes**                                                                                                                                      |
|-------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.0.1       | 2019.08.15 | fixed issue where the CLI returned an error when the stored refresh token had been invalidated and the current cached access token was expired |
| 1.0.0       | 2019.07.23 | first General Availability release                                                                                                             |

-   when you upload a config document from a file via the **config update**
    command, the updated config from the cloud is now saved back to the local
    file; this ensures the return to the user of any auto-generated IDs for the
    permission policies and settings

-   fixed initialization issues with Linux pass storage
  
---
  
DSV CLI Executable for MacOS: Version History (Most Recent First)

| **Version** | **Date**   | **Notes**                                                                                                                                      |
|-------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.0.1       | 2019.08.15 | fixed issue where the CLI returned an error when the stored refresh token had been invalidated and the current cached access token was expired |
| 1.0.0       | 2019.07.23 | first General Availability release                                                                                                             |

-   when you upload a config document from a file via the **config update**
    command, the updated config from the cloud is now saved back to the local
    file; this ensures the return to the user of any auto-generated IDs for the
    permission policies and settings

