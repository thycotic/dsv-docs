[title]: # (Download the CLI)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2200)

# Configuring the CLI

1. Download the Command Line Interface executable files onto each of the workstations where you will operate DevOps Secrets Vault. 
    * Thycotic provides DSV CLI executables for multiple platforms [here](https://dsv.thycotic.com/downloads).
    * Once installed, these CLI executables periodically check the download site for updates and inform the user if an update is available.
1. The executable file name will reflect the OS and 32-bit or 64-bit architecture. Rename the executable to **dsv** or **dsv.exe** to simplify command entry.
1. Place the executable in the file directory location of your choice and note the path.
1. (Optional) Add the Executable Path to the PATH Environment Variable. Adding the location of the executable to your PATH environment variable enables you to invoke `dsv` without specifying its path or having to pre-pend `.\`
    * For Windows, press the Windows key and type "edit environment variables". Select the offered item. In the Environment Variables dialog, under the System Variables section, select the Path and click edit. Add the path to the **dsv** executable (example: C:\Users\<name>\) and save.

    * For Linux or macOS, use *export* to modify the shell profile file, *~.profile* or *~.bash_profile* typically, so that it adds dsv to the PATH on system startup:  *export PATH=~thycotic/cli:$PATH*
1. Enable Autocomplete. Autocomplete is supported for bash, zsh, and fish shells only. To turn on autocomplete for the CLI, run `dsv -install` and restart your shell. Now when you type out the beginning of a command such as `dsv s` and hit tab it will fill out the full command to `dsv secret`

    Autocomplete also helps with expanding the secret path on `dsv secret read`. Put in the beginning of the path, such as `dsv secret read resources` and hit tab to get the next part of the path. If there are multiple matching sub-paths, hit tab twice to print out the available options.

    For example: typing `dsv secret read resources/us-east-` and hitting tab twice will show the output of any secrets below that path. Such as `resources/us-east-1/server  resources/us-east-2/server`