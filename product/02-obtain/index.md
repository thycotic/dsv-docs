[title]: # (Obtain DSV)
[tags]: # (,)
[priority]: # (1200)

# Obtain DSV

Obtaining DevOps Secrets Vault requires only a few straightforward steps.

## Contact Thycotic for Provisioning

Provisioning refers to the steps Thycotic must take to set up your **tenancy**—your cloud secrets vault and the rights to access it.
Contact Thycotic for provisioning:

* { *need to say how to do this* }

### Sandbox Tenants

content?

### Free Version of DevOps Secrets Vault

content?

## Download the Executable for Your OS

Next, download the Command Line Interface executable files for the operating systems you will use when you operate DevOps Secrets Vault.

> Thycotic provides DSV executables for multiple platforms at:
>
> [downloads.secretsvaultcloud.com](https://downloads.secretsvaultcloud.com/)

* Your organization may have standards and policies governing the installation of software, so be sure to speak with your IT staff before installing.

## Rename the Executable

By default, the executable file name as downloaded will reflect the DSV version number. Rename the executable to `thy` or `thy.exe` to simplify command entry.

## Place the Executable

Place the executable in the location of your choice, and as suitable for the device OS.

Note the path to the executable, that is, the location on the storage media, for example, `C:\program files\thycotic`, as you will need this for the PATH environment variable.

## Add the Path to the Executable to the PATH Environment Variable

Add the location of the executable to your PATH environment variable so you can invoke `thy` without specifying its path.

* For Windows, press the Windows key and begin typing “edit the system environment variables.” Windows will almost immediately auto-complete this text and offer to open the Control Panel item used to edit environment variables.

  Select the offered item. Windows will display the System Properties dialog. Use the Environment Variables button to reach the Environment Variables dialog.

  Add the path to the `thy` executable (for example, `C:\thycotic\cli`) to the PATH system variable.

* For Linux or macOS, use `export` to modify the shell profile file, for example `~.profile` or `~.bash_profile`, so that it adds thy to the PATH on system startup:

  `export PATH=~thycotic/cli:$PATH`

