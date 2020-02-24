[title]: # (Obtain DevOps Secrets Vault)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2100)

# Obtain DevOps Secrets Vault

Obtaining DevOps Secrets Vault requires only a few straightforward steps.

## Sign Up with Thycotic for a Tenant

Your **tenant** is your DevOps Secrets Vault cloud account and the rights to access it.

* To sign up, visit Thycotic’s [DevOps Secrets Vault Home Page](https://thycotic.com/products/devops-secrets-vault-password-management/) and fill out and submit the **DevOps Secrets Vault Free** web form.
* Signing up qualifies you for a free, feature-complete try-out version of DevOps Secrets Vault. You begin with the free version to get configured, and upgrade when you need more capacity than the free version’s 250 Secrets and 2500 API calls per month. This allows you to try and test the product before deciding to become a paid subscriber.
* After you sign up, Thycotic emails you instructions for the first half of setup, which is in the Cloud. You will visit Thycotic One to select your region, choose your tenant name, consent to the license terms, and create your initial admin User account.

Come back here for the second half of setup, which involves downloading the CLI executable, initial configuration steps, and configuring for your authentication provider.

## Download the CLI Executable for Your OS

Download the Command Line Interface executable files onto each of the workstations where you will operate DevOps Secrets Vault. 

* Thycotic provides DSV CLI executables for multiple platforms [here](https://dsv.thycotic.com/downloads).
* Once installed, these CLI executables periodically check the download site for updates and inform the user if an update is available.

## Rename the Executable

The executable file name will reflect the OS and 32-bit or 64-bit architecture. Rename the executable to *thy* or *thy.exe* to simplify command entry.

## Place the Executable

Place the executable in the file directory location of your choice and note the path.

## Add the Executable Path to the PATH Environment Variable

While not required, adding the location of the executable to your PATH environment variable enables you to invoke `thy` without specifying its path or having to pre-pend `./`

* For Windows, press the Windows key and begin typing “edit the system environment variables.” Windows will almost immediately auto-complete this text and offer to open the Control Panel item used to edit environment variables.

  Select the offered item. Windows will display the System Properties dialog. Use the Environment Variables button to reach the Environment Variables dialog.

  Add the path to the *thy* executable—for example *C:\Users\name\—to the PATH system variable.

* For Linux or macOS use *export* to modify the shell profile file, *~.profile* or *~.bash_profile* typically, so that it adds thy to the PATH on system startup:  *export PATH=~thycotic/cli:$PATH*

![](./images/spacer.png)

![](./images/spacer.png)
