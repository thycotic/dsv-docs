[title]: # (Report)
[tags]: # (DevOps Secrets Vault,report)
[priority]: # (4855)

# Report Command

The **report** command acts on secrets and groups. All users can generate a report containing their own secrets and groups. Only **administrators** and users with a **policy allowing access to reports/query** can generate a report for other users.

## Secret Reporting

Use the **secret** subcommand to retrieve a list of secrets and secret actions available to a user, group, or role. Running a secret report without flags will generate a list of every secret and action available to the user running the query. Secrets are sorted by the most recent modification.

|Command/Flags|Function|Example|
|-|-|-|
|`report secret`| Retrieves the secrets and secret actions available to a user, group, or role.| `dsv report secret`|
|`--group`| Searches for secrets available to a specified group.| `dsv report secret --group engineers`|
|`--path`| Searches for available secrets within a specified path.| `dsv report secret --path us-east/server01:<.*>`|
|`--role`| Searches for secrets available to a specified role.| `dsv report secret --role automation`|
|`--user`| Searches for secrets available to a specified user.| `dsv report secret --user john`|
|`--limit`| Sets the number of retrieved secrets.| `dsv report secret --limit 25`|

<br>

### Example Secret Queries

#### Personal Secret Query


The following input will return a list of the secrets available to the user performing the query.

**Input:**

```
dsv report secret
```

**Output:**

```json
{
  "data": {
    "UserName": "user",
    "Provider": "thy-one",
    "Created": "2021-01-11T16:07:59Z",
    "LastModified": "2021-01-11T16:07:59Z",
    "CreatedBy": "",
    "LastModifiedBy": "",
    "Version": "0",
    "Secrets": [
      {
        "Actions": ["<.*>"],
        "ID": "",
        "Path": "us-east/server01",
        "Created": "2021-03-25T13:12:13Z",
        "LastModified": "2021-03-25T13:12:13Z",
        "LastModifiedBy": "users:thy-one:user@organization.com",
        "Version": "0"
      }
    ],
    "Home": []
  }
}
```

#### User Secret Query

The following input will return a list of secrets available to the specified user. Note that this query is only available to administrators and users with **reports/query** permission.

**Input**:

```
dsv report secret --user john
```

**Output**:

```json
{
  "data": {
    "UserName": "john",
    "Provider": "thy-one",
    "Created": "2021-01-11T16:07:59Z",
    "LastModified": "2021-01-11T16:07:59Z",
    "CreatedBy": "",
    "LastModifiedBy": "",
    "Version": "0",
    "Secrets": [
      {
        "Actions": ["<.*>"],
        "ID": "",
        "Path": "us-east/server01",
        "Created": "2021-03-25T13:12:13Z",
        "LastModified": "2021-03-25T13:12:13Z",
        "LastModifiedBy": "users:thy-one:john@example.com",
        "Version": "0"
      }
    ],
    "Home": []
  }
}
```

## Group Reporting

Use the **group** subcommand to retrieve a list of groups associated with a user or role. Running a group report without flags will generate a list of groups associated with the user running the query. 

|Command/Flags|Function|Example|
|-|-|-|
|`report group`| Retrieves a list of groups associated with a user or role.| `dsv report group`|
|`--role`| Searches for the groups associated with a specified role.| `dsv report group --role automation`|
|`--user`| Searches for the group memberships of a specified user.| `dsv report group --user john`|
|`--limit`| Sets the number of retrieved groups.| `dsv report group --limit 25`|

<br>

### Example Group Queries

#### Personal Group Query

The following input will return a list of the groups to which the user performing the query belongs.

**Input:**

```
dsv report group
```

**Output:**

```json
{
  "data": {
    "UserName": "user",
    "Provider": "thy-one",
    "Created": "2021-01-11T16:07:59Z",
    "LastModified": "2021-01-11T16:07:59Z",
    "CreatedBy": "",
    "LastModifiedBy": "",
    "Version": "0",
    "group": [
      {
        "Name": "accountadmins",
        "Since": ""
      }
    ]
  }
}
```

#### User Group Query

The following input will return a list of groups to which the specified user belongs. Note that this query is only available to administrators and users with **reports/query** permission.

**Input**:

```
dsv report group --user john
```

**Output**:

```json
{
  "data": {
    "UserName": "john",
    "Provider": "thy-one",
    "Created": "2021-01-11T16:07:59Z",
    "LastModified": "2021-01-11T16:07:59Z",
    "CreatedBy": "",
    "LastModifiedBy": "",
    "Version": "0",
    "group": [
      {
        "Name": "engineers",
        "Since": ""
      }
    ]
  }
}
```