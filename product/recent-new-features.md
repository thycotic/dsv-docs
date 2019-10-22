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

## Caching Improvements

**Server side policy caching** has been updated to better handle permission updates.

## CLI Audit

You can now find and examine audit logs via the CLI. Previously, this was only possible through the API. 

