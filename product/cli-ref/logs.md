[title]: # (Logs)
[tags]: # (DevOps Secrets Vault,DSV,logging)
[priority]: # (4850)

# Logs

DSV logs can be searched with `dsv logs` followed by `--startdate YYYY-MM-DD`.

|Flag|Function|
|---|---|
|`--actions`| Searches for a given action within DSV.|
|`--cursor`| A cursor value is given when the logs returned are longer than the limit. See below for example usage.|
|`--enddate`| Along with `--startdate`, sets the time frame for search.|
|`--limit`| Sets the maximum number of results per cursor.|
|`--path`| Searches for actions within a given path.|
|`--principal`| Searches for actions taken with a given principal.|

## Usage

```bash
dsv logs


```
--If the logs are longer than the limit, the CLI will return a `--cursor` value. Input the flag and the value to continue the list of logs.