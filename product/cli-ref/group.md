[title]: # (Group)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4400)

# Group

A Group facilitate the application of the same policies to all members of a given set of Users.

## Commands that Act on Groups
 
![](./images/spacer.png)

| Command        | Action                         |
| -------------- | ------------------------------ |
| create         | create a Group in the vault    |
| add-members    | add members to a Group         |
| read           | read a Group’s details         |
| update         | update a Group                 |
| delete-members | remove members from a Group    |
| delete         | delete a Group                 |
| restore        | restore a Group (if within 72 hours of deletion and not hard deleted) |

![](./images/spacer.png)

## Examples

### Create

#### File Example

This example command would create a Group named **admins** from a file **data.json** containing *{"groupName": "admins"}* (or same with single-quote marks, for Powershell) and located in the **tmp** folder:

```BASH
dsv group create --data @/tmp/data.json

{
  "groupName": "admins",
  "id": "2ce6754d-afbc-43a9-bfd4-3b7ec61170a0",
  "members": null,
  "metaData": null
}
```

#### Direct Data Example

This example would create a Group without referencing a file:

```BASH
dsv group create -data {"groupName": "admins"} 
{
  "groupName": "admins",
  "id": "2ce6754d-afbc-43a9-bfd4-3b7ec61170a0",
  "members": null,
  "metaData": null
}
```

Note that in Powershell, single quotes are required and double quotes escaped, like this:

```BASH
dsv group  create --data  '{\"groupName\": \"admins\"}'
```

#### Wizard Example

A group can also be created using the wizard:
```BASH
dsv group create
```

### Find Group Membership

To see what Groups the user Billy belongs to, you would use:

```BASH
dsv user groups --username billy
{
  "groups": [
    {
      "groupName": "admins"
    }
  ],
  "name": "billy"
}
```

### Add-Members

Add members to a Group similarly to this example, wherein the file *newmember.json* contains: *{"memberNames": [ "billy",”larry’]}*

```BASH
dsv group add-members --group-name admins --data '@/tmp/newmember.json

{
  "memberNames": ["billy", "larry"]
}
```
 
### Read

This example demonstrates how to read a Group:

```BASH
dsv group read --group-name admins
{
  "groupName": "admins",
  "id": "2dc756d6-ba71-44e9-94e9-f822e0f7ca3f",
  "members": ["larry"],
  "metaData": null
}
```

### Update | Assign Group to Policy

This example would assign the **admins** Group to an existing policy at the path *secrets:servers:us-west*:

```BASH
dsv policy update --actions "<.*>" --subjects groups:admins --path secrets/servers/us-west
```

Note that you can designate paths with either of the colon : or forward slash / characters.

### Delete-Members

To remove members from a Group, follow this example, wherein *deletemembers.json* contains: *{"memberNames": ["billy"]}*

```BASH
dsv group delete-members --group-name admins --data @/tmp/deletemembers.json
<no response>
```

Note that this does not delete the user objects that were members. It simply makes those user objects no longer members of the Group.

### Delete

To delete a Group, you would follow this example:

```BASH
dsv group delete --group-name admins
<no response>
```

When you delete a Group, it will no longer be usable. However, with the soft delete capacity of DSV, you have 72 hours to use the *restore* command to undelete the Group. After 72 hours, the Group will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the *delete* command’s `--force` flag.

### Restore

Up to 72 hours after you delete a Group (but not if you hard deleted it using the `--force` flag), you can restore it:

```bash
dsv group restore --group-name admins
```

![](./images/spacer.png)

![](./images/spacer.png)


