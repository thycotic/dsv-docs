[title]: # (Download the CLI)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2200)

# Download the CLI Executable for Your OS

Download the Command Line Interface executable files onto each of the workstations where you will operate DevOps Secrets Vault. 

* Thycotic provides DSV CLI executables for multiple platforms [here](https://dsv.thycotic.com/downloads).
* Once installed, these CLI executables periodically check the download site for updates and inform the user if an update is available.

## Rename the Executable

The executable file name will reflect the OS and 32-bit or 64-bit architecture. Rename the executable to *thy* or *thy.exe* to simplify command entry.

## Place the Executable

Place the executable in the file directory location of your choice and note the path.

## Add the Executable Path to the PATH Environment Variable

While not required, adding the location of the executable to your PATH environment variable enables you to invoke `thy` without specifying its path or having to pre-pend `.\`

* For Windows, press the Windows key and type "edit environment variables". Select the offered item.

  In the Environment Variables dialog, under the System Variables section, select the Path and click edit.  

  Add the path to the *thy* executable—for example *C:\Users\<name>\ and save.

* For Linux or macOS use *export* to modify the shell profile file, *~.profile* or *~.bash_profile* typically, so that it adds thy to the PATH on system startup:  *export PATH=~thycotic/cli:$PATH*