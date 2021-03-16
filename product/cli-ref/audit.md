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
|`--principal`| Searches for a specific principal or user within DSV.| `--principal users:thy-one:your.username@organization.com` |
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

## Available Audit Logs

|Path|Method|Status|Description|
|-|-|-|-|
|clients|POST|201|Log when client is created successfully|
|clients:{clientId}|GET|200|Log when client is read|
|clients:bootstrap:{clientId}|GET|200|Log when client credential is read|
|clients|GET|200|Log when client search is performed|
|clients:{clientId}|DELETE|200|Log when client is deleted|
|clients:{clientId}:restore|GET|200|Log when client is restored|
|config:auth|POST|201|Log when new auth provider is saved|
|config:auth:{name}|GET|200|Log when auth provider is read
|config:auth:{name}|PUT|200|Log when auth provider is updated|
|config:auth:{name}:version:{version}|GET|404,200|Log when auth provider is read by version|
|config:auth|GET|200|Log when auth provider is searched|
|config:auth:{name}:rollback:{version}|PUT|404,200|Log when auth provider config is rolled back|
|config:auth:{name}|DELETE|200|Log when auth provider config is deleted|
|config:auth:{name}:restore|GET|200|Log when auth provider config is restored|
|config:policies:{path}|GET|200|Log when policy is read|
|config:policies:{path}:version:{version}|GET|404,200|Log when policy is ready by version|
|config:policies|POST|201|Log when policy is created|
|config:policies:{path}|PUT|200|Log when policy is updated|
|config:policies:{path}:rollback:{version}|PUT|404,200|Log when policy is rolled back|
|config:policies|GET|200|Log when policy is searched|
|config:policies:{path}|DELETE|200|Log when policy is deleted|
|config:siem|POST|201|Log when siem endpoint is registered|
|config:siem:{name}|PUT|200|Log when siem endpoint is updated|
|config:siem:{name}|GET|200|Log when siem endpoint is read|
|config:siem:{name}|DELETE|200|Log when siem endpoint is deleted|
|crypto:key:{path}|POST|201|Log when new encryption key is created|
|crypto:rotate|POST|201|Log when data and key are rotated|
|crypto:key:{path}|GET|200|Log when key metadata is read|
|crypto:key:{path}|DELETE|204|Log when key is deleted|
|crypto:key:{path}:restore|PUT|204|Log when key is restored|
|crypto:encrypt|POST|200|Log when data is encrypted|
|crypto:decrypt|POST|200|Log when data is decrypted|
|engines|POST|201|Log when dsv engine is created|
|engines:{name}:ping|POST|200|Log when an engine is pinged|
|engines:{name}|GET|200|Log when an engine is read|
|engines:{name}|DELETE|200|Log when an engine is deleted|
|pools|POST|201|Log when a pool is created|
|pools:{name}|GET|200|Log when a pool is read|
|pools:{name}|DELETE|204|Log when a pool is deleted|
|groups|POST|201|Log when a group is created|
|groups:{name}:members|POST|200|Log when a group member is added|
|groups:{name}|GET|200|Log when a group is read|
|users:{name}:group|GET|200|Log when group members are read|
|groups:{name}:members|DELETE|204|Log when group members are deleted|
|groups:{name}|DELETE|200|Log when group is deleted|
|groups:{name}:restore|GET|200|Log when group is restored|
|groups|GET|200|Log when groups are searched|
|pki:register|POST|201|Log when CA root is saved|
|pki:root|POST|200|Log when CA root is generated|
|pki:sign|POST|200|Log when certificate is signed|
|pki:leaf|POST|200|Log when leaf certificate & key are created|
|pki:ssh-cert|POST|200| Log when SSH cert is saved/generated|
|roles|POST|201|Log when role is created|
|roles:{name}|PUT|200|Log when role is updated|
|roles:{name}|GET|200|Log when role is read|
|roles:{name}:version:{version}|GET|200|Log when role is read by version|
|roles|GET|200|Log when roles are searched|
|roles:{name}|DELETE|200|Log when role is deleted|
|roles:{name}:restore|GET|200|Log when role is restored|
|task:status:{id}|GET|200|Log when task status is read|
|token|POST|200|Log when user authenticates successfully|
|revoke:{refreshtoken}|POST|204|Log when a refresh token is revoked|
|token|POST|0|Log when user authentication attempt fails|
|users:{name}|PUT|200|Log when a user is updated|
|users|POST|201|Log when a user is created|
|users:{name}:password|POST|200|Log when user password is updated|
|users:{name}|GET|200|Log when user is read|
|users:{name}:version:{version}|GET|200|Log when user is read by version|
|users|GET|200|Log when users are searched|
|users:{name}|DELETE|200|Log when user is deleted|
|users:{name}:restore|GET|200|Log when user is restored|
|config|GET|500,404,200|Log when config is read|
|config:version:{version}|GET|404,500,200|Log when config is read by version|
|config|POST|400,500,201|Log when config is created or updated|
|secrets:{path|id}|GET|404,200|Log when secret is read|
|secrets:{path|id}:version:{version}|GET|404,200|Log when secret is read by version|
|secrets:{path|id}:rollback:{version}|PUT|404,200|Log when secret is rolled back|
|secrets:{path|id}::description|GET|404,200|Log when secret is described|
|secrets:{path}::listpaths|GET|0|Log when secret paths are listed [disabled]|
|secrets:{path}|POST|201|Log when secret is created|
|secrets:{path|id}|PUT|200| Log when secret is updated|
|secrets:{path|id}|DELETE|200|Log when secret is deleted|
|secrets:{path|id}:restore|GET|200|Not logged|
|secrets|GET|200|Log when secrets are searched|
|home:{principal}:{path}|GET|404,200|Log when home secret is read|
|home:{principal}:{path}|POST|201|Log when home secret is created|
|home:{principal}:{path}|PUT|200|Log when home secret is updated|
|home:{principal}:{path}|DELETE|200|Log when home secret is deleted|
|home:{principal}:{path}::description|GET|404,200|Log when home secret is described|
|home:{principal}|GET|200|Log when home is searched|
|home:{principal}:{path}:version:{version}|GET|404,200|Log when home secret is read by version|
|home:{principal}:{path}:restore|GET|--|Not logged|
|home:{principal}:{path}:rollback:{version}|PUT|--|Not logged|
