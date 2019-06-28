[title]: # (Download and Install DSV)
[tags]: # (,)
[priority]: # (1200)

# Download and Install DSV

Setting up DSV requires only a few steps.

## Download the Executable for Your OS

Thycotic provides DSV executables for multiple platforms at [downloads.secretsvaultcloud.com](https://downloads.secretsvaultcloud.com/).

* Your organization may have standards and policies governing the installation of software, so be sure to speak with your IT staff before installing.

Download the latest version of the DSV CLI for the OS of the workstation on which you intend to install DSV.

## Install the Executable

Installation consists of renaming the downloaded file, placing it in a suitable location, and adding that location to your **PATH** environment variable.

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

