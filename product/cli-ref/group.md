[title]: # (Group)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1825)

# Group

A Group facilitate the application of the same policies to all members of a given set of Users.

## Commands that Act on Groups
  
---
  
| Command        | Action                         |
| -------------- | ------------------------------ |
| add-members    | add members to a group         |
| create         | create a group in the vault    |
| read           | read a group’s details         |
| update         | update a group                 |
| delete-members | remove members from a group    |
| delete         | delete a group                 |

## Examples

### Add-Members

Add members to a group similarly to this example, wherein the file `newmember.json` contains: `{"memberNames": [ "billy",”larry’]}`

```bash
thy group add-members --group-name admins --data '@/tmp/newmember.json

{
  "memberNames": ["billy", "larry"]
}
```
 

### Create

This example command would create a group named **admins** from a file **data.json** containing `{"groupName": "admins"}` (or same with single-quote marks, for Powershell) and located in the **tmp** folder:

```bash
thy group create --data @/tmp/data.json

{
  "groupName": "admins",
  "id": "2ce6754d-afbc-43a9-bfd4-3b7ec61170a0",
  "members": null,
  "metaData": null
}
```

This example would create a group without referencing a file:

```bash
thy group create -data {"groupName": "admins"} 
{
  "groupName": "admins",
  "id": "2ce6754d-afbc-43a9-bfd4-3b7ec61170a0",
  "members": null,
  "metaData": null
}
```

Note that in Powershell, single quotes are required and double quotes escaped, like this:

`thy group  create --data  '{\"groupName\": \"administrators\"}'`


To see what groups the user Billy belongs to, you would use:

```bash
thy user groups --username billy
{
  "groups": [
    {
      "groupName": "admins"
    }
  ],
  "name": "billy"
}
```

### Read

This example demonstrates how to read a group:

```bash
thy group read --group-name admins
{
  "groupName": "admins",
  "id": "2dc756d6-ba71-44e9-94e9-f822e0f7ca3f",
  "members": ["larry"],
  "metaData": null
}
```

### Update

This example would assign the **admins** group to an existing policy at the path `secrets:servers:us-west`:

`thy policy update --actions "<.*>" --subjects groups:admins --path secrets/servers/us-west`

Note that you can designate paths with either of the colon : or forward slash / characters.

### Delete-Members

To remove members from a group, follow this example, wherein `deletemembers.json` contains: `{"memberNames": ["billy"]}`

```bash
thy group delete-members --group-name admins --data @/tmp/deletemembers.json` 
<no response>
```

Note that this does not delete the user objects that were members. It simply makes those user objects no longer members of the group.

### Delete

To delete a group, you would follow this example:

```bash
thy group delete --group-name admins
<no response>
```



  

