[title]: # (Break Glass)
[tags]: # (DevOps Secrets Vault,DSV,break glass)
[priority]: # (7000)

# Break Glass

The **Break Glass** feature is intended for emergency use if the **Super Administrator** account credentials are lost or compromised. Break Glass allows a selected group of DSV users to recover Super Administrator access.

When **Break Glass** is first setup, DSV distributes **shares** of the Super Administrator credentials to users who will have Super Administrator access after Break Glass is triggered. If enough shares are combined, the users can change ownership of the Super Administrator account to a new group of admins. 

To trigger a Break Glass event, a user will run the `breakglass` command along with the minimum number of shares needed to recover the account. 

## Commands and Flags

|Command|Usage||
|---|---|---|
|`breakglass`| Main command to configure and apply the Break Glass feature.||
|`apply`| Subcommand to trigger the break glass event and recover Super Admin credentials.||
||`--shares`| Flag used to pass in the distributed secret `shares` needed to recover Super Admin credentials. Pass the distributed shares for this flag.|
|`generate`| Subcommand to enable the break glass feature.||
||`--min-number-of-shares`| Flag used to set the minimum number of distributed `shares` needed to recover Super Admin credentials. Pass in a numerical value for this flag.|
||`--new-admins`| Flag used to choose who the new administrators will be after the Break Glass event. Pass in a list of usernames for this flag.|
|`status`| Subcommand to return current status of Break Glass implementation.||
<br>

## Break Glass Setup

To setup Break Glass, enter the `dsv breakglass generate` command along with the `--new-admins` and `--min-number-of-shares` flags. The following example will require three shares to trigger a Break Glass event and give the users username1 and username2 administrative rights following the event. 

>**NOTE**: The number of `new-admins` must be greater than or equal to the number of `min-number-of-shares`.

Example:

```
dsv breakglass generate --new-admins 'username1,username2,username3,username4' --min-number-of-shares 3
```

The `share` values are sent to each new admin. The admin can access this value using the command: **`dsv home __breakglass_share`**

## Trigger Break Glass

To trigger a break glass event, a user must collect the minimum number of share values from users who are designated as new admins (`new-admins`). The new admins can access the value using the command: **`dsv home __breakglass_share`**. The share values must then be entered using the command:

```
dsv breakglass apply --shares '{share1},{share2},{share3}'
```

The new admins now have Super Administrator access.