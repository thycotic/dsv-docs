[title]: # (Audit)
[tags]: # (DevOps Secrets Vault,DSV,logging,audit)
[priority]: # (4850)

# Audit Command

DSV audit logs can be searched with the `dsv audit` command followed by the **required** `--startdate YYYY-MM-DD` flag.

## Flags

|Flag|Function|Example|
|---|---|---|
|`--actions`| Searches within audit logs for a specific CRUD action. Use the values: POST, GET, PUT, PATCH, DELETE. If omitted, all actions will return.| `--actions PUT`|
|`--cursor`| A cursor value is given when the number of events returned exceed the display limit. Include the returned cursor value in the next query to continue viewing the log. See below for example usage.| `--cursor <string>`
|`--enddate`| Along with `--startdate`, sets the time frame for search. If omitted, `enddate` will return all events from the startdate to the search date. **Make sure to use the YYYY-MM-DD format. You must include a zero before single-digit dates.**| `--enddate 2021-02-01`|
|`--limit`| Sets the maximum number of results per cursor. If omitted, `limit` will default to 25.| `--limit 10`|
|`--path`| Searches for actions within a given path. If omitted, all `paths` will return.| `--path secrets:us-east:server01`
|`--principal`| Searches for a specific principal or user within DSV. If omitted, all principals will return.| `--principal users:thy-one:your.username@organization.com` |
|`--startdate`| Along with `--enddate`, sets the time frame for search. **Make sure to use the YYYY-MM-DD format. You must include a zero before single-digit dates.** This flag is required.| `--startdate 2020-08-21`|

## Usage Examples

### Basic Unfiltered Query

```
dsv audit --startdate 2021-01-01
```

An audit log of all actions in every path from January 1st, 2021 to the present date is returned.

### Simple Limited Query

```
dsv audit --startdate 2021-01-01 --enddate 2021-02-02 --limit 5
```

An audit log of the most recent five actions in every path from January 1st, 2021 to February 2nd, 2021 is returned.

### Cursor Query

If the logs are longer than the limit, the CLI will return a long `--cursor` string. Copy the cursor value and repeat the previous input with the addition of `--cursor <returned string>` to continue listing the logs.

#### Initial Input Returning a Cursor Value

```
dsv audit --stardate 2021-01-01 --enddate 2021-02-02 --limit 2
```

#### Example Output Returning Cursor

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

#### Example Input with Cursor

```
dsv audit --stardate 2021-01-01 --enddate 2021-02-02 --limit 2 --cursor MGJiYmYxZmItZjlhMS00NjY1LWEyN2YtNDgwM2E3MjExMjRh.AT0HNoBK4m4rE_XkhWoXImQyjbX8hrSHQiXM06qRIQ8KgZAU21Kdb-bmur6kK85N34z2e5LEhSoEIAV3a5bhgkFbE5a9W78iwg
```