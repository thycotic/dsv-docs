[title]: # (New This Month)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (2105)
[display]: # (none)

# New This Month: November

*Use this file only for months with extensive changes.*

These changes and improvements to DSV became effective on Tuesday, November 19.

## Soft Delete

When you delete a Secret, Role, User, Group, Policy, or Authentication Provider, as before the record will no longer be usable. However, with the new soft delete capacity of DSV, you now have 72 hours to use the new restore command to in effect undelete the item. After 72 hours, the item will no longer be retrievable.

Should you want to perform a hard delete, precluding any restore operation, you can use the delete command’s `--force` flag.

## Improved Availability and Recoverability

Recent changes in DSV’s cloud architecture back uptime of 99.999 percent, with continuous backup enabling fail-over to a hot stand-by in under one minute. Updates to the EULA (your End User License Agreement) detail the new SLA for availability.


