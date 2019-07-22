[title]: # (Obtain DevOps Secrets Vault)
[tags]: # (,)
[priority]: # (1200)

# Obtain DevOps Secrets Vault

Obtaining DevOps Secrets Vault requires only a few straightforward steps.

## Sign Up with Thycotic for Provisioning

Provisioning refers to the steps Thycotic must take to set up your **tenancy**—your cloud secrets vault and the rights to access it.

* To sign up, visit Thycotic’s [DevOps Secrets Vault Home Page](https://thycotic.com/products/devops-secrets-vault-password-management/) and fill out and submit the **DevOps Secrets Vault Free** web form.
* Signing up qualifies you for a free, feature-complete try-out version of DevOps Secrets Vault. You begin with the free version to get configured, and upgrade when you need more capacity than the free version’s 250 secrets and 2500 API calls per month. This allows you to try and test the product before deciding to become a paid subscriber.
* After you sign up, Thycotic emails you instructions for the first half of setup, which is in the Cloud. You will visit Thycotic One to select your region, choose your tenant name, consent to the license terms, and create your initial admin user account.

You come back here for the second half of setup, which involves downloading the CLI executable, initial configuration steps, and configuring for your authentication provider.

## Download the Executable for Your OS

After you have been provisioned, download the Command Line Interface executable files onto each of the workstations where you will operate DevOps Secrets Vault.

* Thycotic provides DSV executables for multiple platforms [here](dsv.thycotic.com/downloads).

* Your organization may have standards and policies governing the installation of software, so be sure to speak with your IT staff before installing.

## Rename the Executable

By default, the executable file name as downloaded will reflect the OS and 32-bit or 64-bit architecture. Rename the executable to `thy` or `thy.exe` to simplify command entry.

## Place the Executable

Place the executable in the location of your choice, and as suitable for the device OS.

Note the path to the executable, that is, the location on the storage media, for example, `C:\program files\thycotic`, as you will need this for the PATH environment variable of the OS.

## Add the Executable Path to the PATH Environment Variable

Add the location of the executable to your PATH environment variable so you can invoke `thy` without specifying its path.

* For Windows, press the Windows key and begin typing “edit the system environment variables.” Windows will almost immediately auto-complete this text and offer to open the Control Panel item used to edit environment variables.

  Select the offered item. Windows will display the System Properties dialog. Use the Environment Variables button to reach the Environment Variables dialog.

  Add the path to the `thy` executable (for example, `C:\thycotic\cli`) to the PATH system variable.

* For Linux or macOS, use `export` to modify the shell profile file, for example `~.profile` or `~.bash_profile`, so that it adds thy to the PATH on system startup:

  `export PATH=~thycotic/cli:$PATH`
