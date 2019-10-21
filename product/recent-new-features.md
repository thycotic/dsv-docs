[title]: # (Recent New Features)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (9005)

# Recent New Features: October

These changes and improvements to DSV became effective on Tuesday, October 22.

## Partial Updates to Secrets Fields via New Update Flags

With the update command’s new flags, *--data*, *-- desc*, and *--attributes*, you can update each of the three parts of a Secret separately from the other parts. 

The Secret update command also has a new *--overwrite* flag. This is a Boolean flag.

* If you use the *--overwrite* flag with the *update* command, all existing fields within the Secret’s data object will be replaced by the new fields specified by the value of the *update* command’s *--data* flag.
* If you do not use the *--overwrite* flag, the value of the *update* command’s *--data* flag will be merged with the existing fields in the Secret’s data object.
  * The *--update* flag only applies to the fields within the data object, not their attributes. That is, if you use *update* to specify attributes for a particular existing field within a Secret’s data object, the attributes you specify will entirely replace all the existing attributes of that field, not merge with them. 

## Data Field of Secrets Now More Flexible

Related to the new flags for the *update* command, the *data* section of a Secret may now have as many fields as you specify.

## Caching Improvements

Caching behavior updates ensure that among multiple users who might have cached information about the same Secret at the same time, changes by one of the users will be seen by the other users, should they happen to make a query the results of which would include the changed element.

## CLI Audit

You can now find and examine audit logs via the CLI. Previously, this was only possible through the API. 

## Soft Delete

When you delete a Secret, Role, User, or Group, as before it will no longer be usable. However, with the new soft delete capacity of DSV, you now have 72 hours to use the new restore command to in effect undelete the item. After 72 hours, the item will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the delete command’s --force flag.
