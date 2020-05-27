[title]: # (Policy)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4700)

# Policy

Policies control access to resources and authorization to act on resources, such as to change them, via **permissions**.
DevOps Secrets Vault permissions are foundational for proper operation and security.

To get a json encoded list of all Policies, use:

```BASH
thy policy search
```

You can add a query item to search Policies by path:

```BASH
thy policy search secrets/databases
```

or

```BASH
thy policy search –query secrets/databases
```

A typical Policy looks like this:

```yaml
{
"created": "2019-09-24T18:12:26Z",
"createdBy": "users:thy-one:admin@company.com",
"id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
"lastModified": "2019-09-24T20:13:53Z",
"lastModifiedBy": "users:thy-one:admin@company.com",
"path": "secrets:servers:us-west",
"permissionDocument": [
{
"actions": ["read"],
"conditions": {},
"description": "",
"effect": "allow",
"id": "xxxxxxxxxxxxxxxxxxxx",
"meta": null,
"resources": ["secrets:servers:us-west:<.*>"],
"subjects": ["groups:west admins"]
}
],
"version": "5"
}
```

The syntax supports wildcards via the <.*> construct.

![](./images/spacer.png)

| **Element**        | **Definition**                                                                                                                                                                                                                                          |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| actions            | a list of possible actions on the resource including create, read, update, delete, list, share, and assign (regular expressions and list supported)                                                                                                     |
| conditions         | an optional CIDR range to lock down access to a specific IP range                                                                                                                                                                                       |
| description        | human friendly description of the Policy intent                                                                                                                                                                                                         |
| effect             | whether the Policy is allowing or preventing access; valid values are allow and deny                                                                                                                                                                    |
| id                 | system-generated unique identifier to track changes to a particular Policy                                                                                                                                                                              |
| resources          | the resource path defining the targets to which the permissions apply; a resource path prefixes the entity type (Secrets, clients, Roles, Users, config, config:auth, config:policies, audit, system:log) to a colon delimited path to the resource.    |
| subjects           | the Policy provides authorization to these entiries.  Includes Users, Roles, and Groups                                                                                                                                                       |

![](./images/spacer.png)

## Policy Evaluation

To correctly evaluate permission Policies, you must know the rules that apply to permissions.

* Values for permission properties may optionally be specified using a regular expression enclosed in angle brackets `<>`. For example,
a subject entry could be written as `["users:<bob|alice>"]`. Here, users `bob` and `alice` are specified. A longer alternative would be
`["users": "bob", "users": "alice"]`.
* Permissions are cumulative.
  * If there is a top level permission for the path secrets:servers:<.\*> that grants a User **write** access, then even if they are only granted **read** access at the resource path secrets:servers:webservers:<.\*>, they will still have write access due to the top level implicit match.
* An `effect` must be specified and can either be `allow` or `deny`.
* An explicit deny trumps an explicit or implicit allow.
* At least one action must be listed in an array. Actions are explicit. A User assigned **update** and **read** will not automatically have **create** for the resource path.
* For actions, the wildcard form `<.*>` replaces any other values, since it is an all-inclusive form.
* Invalid actions are not allowed, unless there is a wildcard element. Valid actions are `share, delete, update, create, read, assign, list`.
* The **list** action has a special behavior.
  * First, **list** (search) is global—it runs across all items of an entity, not limited to paths and sub-paths.
  * Second, to grant a User an ability to search entities via *list*, use the root of the entity if you want *list* to include other entities and actions within the same Policy. The root entity, for example, is secrets, with no other characters following.
* At least one subject must be listed in an array. A prefix is required. For example, a valid subject is `"users:bob"`. Valid prefixes are [groups, roles, users].
* A wildcard could be written as a standard `<.*>` form, but also as `.*` or `*` for convenience. The backend automatically converts it to `<.*>`.
* Subjects and actions are automatically converted to lower case upon save.

## Policy Examples

### Deny Access at a Lower Level

**Case:** Subjects need access to Secrets for an environment, but that logical environment contains a more restricted area.

**Solution:** Two Policies. The first provides the Subjects (*developer1@thycotic.com|developer2@thycotic.com*) general access to the Secrets resources at the path *secrets:servers:us-east-1:<.*>*.

The direct command to create this policy is `thy policy create --path secrets:servers:us-east-1 --actions <.*> --desc 'Developer Policy' --subjects 'users:<developer1@thycotic.com|developer2@thycotic.com>' --effect allow`  With the trickiest part being to remember the "secrets" prefix on the path.

```yaml
path: secrets:servers:us-east-1
permissionDocument:
- id: xxxxxxxxxxxx
description: Developer Policy.
subjects:
- users:<developer1@thycotic.com|developer2@thycotic.com>
actions:
- "<read|delete|create|update|share>"
effect: allow
resources:
- secrets:servers:us-east-1:<.*>
```

The second Policy adds an explicit *deny* with a more specific path to deny access at the more privileged level, as in the following example.

```yaml
path: secrets:servers:us-east-1
permissionDocument:
- id: xxxxxxxxxxxx
description: Developer Deny Policy.
subjects:
- users:<developer1@thycotic.com>
actions:
- "<.*>"
effect: deny
resources:
- secrets:servers:us-east-1:production:<.*>
```

### Allow Users to Assign Specific Roles

**Case:** A User needs to assign Roles when they create client credentials, but must not be able to self-elevate by assigning an admin level Role.

**Solution:** Use a naming convention when creating Roles and specify a prefix with a wildcard to only allow Users to assign Roles that match the naming convention, as modeled in the following example.

```yaml
- id: erji23829828
description: Limited Role Assignment Policy.
subjects
- users:developer@thycotic.com
- roles:onboarding-role
actions:
- "<assign>"
effect: allow
resources:
- roles:dev-role-<.*>
```

### Allow Users to List Specific Entities

**Case:** A User needs to read and list entities within a Policy.

**Solution:** Add a list action and the root of the entity used for searching.

In the example below, *roles* is the entity for reading and searching (list action). In the **resources** section, *roles:dev-role-<.*>* is used for reading, while *roles* is used for searching.

```yaml
- id: xxxxxxxxxxxx
description: Limited Role Policy.
subjects:
- users:developer@thycotic.com
- roles:onboarding-role
actions:
- "<read|list>"
effect: allow
resources:
- roles:dev-role-<.*>
- roles
```

The syntax of the latter is important. In general, the root form of an entity has no * after the entity name, or anything besides the name.

### Delegate Policy Authority

**Case:** An admin wants to delegate control to various team leads at a sub-path.

**Solution:** Under Resources, add config:policies followed by the resource path.

```yaml
- id: xxxxxxxxxxxx
description: Delegate sub-domains.
subjects:
- - users:<developer1@thycotic.com|developer2@thycotic.com>
actions:
- "<create|read|delete|update>"
effect: allow
resources:
- secrets:servers:<.*>
- config:policies:secrets:servers:<.*>
```

Now the developers can create Policies below the *secrets:servers:* path; for example, developer1 can create Policies for *secrets:servers:webservers* and developer2 can do the same at *secrets:servers:databases*.

![](./images/spacer.png)

![](./images/spacer.png)
