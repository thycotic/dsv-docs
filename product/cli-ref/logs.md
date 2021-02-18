[title]: # (Logs)
[tags]: # (DevOps Secrets Vault,DSV,logging)
[priority]: # (4850)

# Logs Command

DSV logs can be searched with the `dsv logs` command followed by the **required** `--startdate YYYY-MM-DD` flag.

## Flags

|Flag|Function|
|---|---|
|`--actions`| Searches for a given action within DSV. Acceptable values are "GET", "PUT", "POST", and "DELETE". If omitted, all `actions` will return.|
|`--cursor`| A cursor value is given when the number of logs returned exceed the display limit. Include the returned cursor value in the next query to continue viewing the log. See below for example usage.|
|`--enddate`| Along with `--startdate`, sets the time frame for search. If omitted, `enddate` will return all logs from the startdate to the search date.|
|`--limit`| Sets the maximum number of results per cursor. If omitted, `limit` will default to 25.|
|`--path`| Searches for actions within a given path. If omitted, all `paths` will return.|
|`--principal`| Searches for actions taken with a given principal. If omitted, all `principals` will return.|
|`--startdate`| Along with `--enddate`, sets the time frame for search. **Make sure to use the YYYY-MM-DD format including zeros (0) for single-digit dates.** This flag is required.|

## Usage Examples

### Basic Unfiltered Query

```
dsv logs --startdate 2021-01-01
```

A log of all actions in every path and principal from January 1st, 2021 to the present date is returned.

### Simple Limited Query

```
dsv logs --stardate 2021-01-01 --enddate 2021-02-02 --limit 5
```

A log of the most recent five actions in every path and principal from January 1st, 2021 to February 2nd, 2021 is returned.

### Cursor Query

If the logs are longer than the limit, the CLI will return a long `--cursor` string. Copy the cursor value and repeat the previous input with the addition of `--cursor <returned string>` to continue listing the logs.

#### Initial input

```
dsv logs --stardate 2021-01-01 --enddate 2021-02-02 --limit 2
```

#### Example Output

```JSON
"cursor": "MGJiYmYxZmItZjlhMS00NjY1LWEyN2YtNDgwM2E3MjExMjRh.AT0HNoBK4m4rE_XkhWoXImQyjbX8hrSHQiXM06qRIQ8KgZAU21Kdb-bmur6kK85N34z2e5LEhSoEIAV3a5bhgkFbE5a9W78iwg",
    "data": [
    {
      "action": "POST",
      "created": "2021-01-26T16:48:53.502387353Z",
      "id": "",
      "ipaddress": "11.111.11.***",
      "message": "user attempting login: example@thycotic.com",
      "path": "token",
      "principal": "",
      "principalItemId": "",
      "status": 0,
      "tenant": "tenantIDstring",
      "tenantName": "yourorg"
    },
    {
      "action": "POST",
      "created": "2021-01-26T16:48:53.839114046Z",
      "id": "",
      "ipaddress": "11.111.11.***",
      "message": "unable to find provider with specified name.",
      "path": "token",
      "principal": "",
      "principalItemId": "",
      "status": 0,
      "tenant": "tenantIDstring",
      "tenantName": "yourorg"
```

#### Cursor Input

```
dsv logs --stardate 2021-01-01 --enddate 2021-02-02 --limit 2 --cursor MGJiYmYxZmItZjlhMS00NjY1LWEyN2YtNDgwM2E3MjExMjRh.AT0HNoBK4m4rE_XkhWoXImQyjbX8hrSHQiXM06qRIQ8KgZAU21Kdb-bmur6kK85N34z2e5LEhSoEIAV3a5bhgkFbE5a9W78iwg
```