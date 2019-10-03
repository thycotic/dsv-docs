[title]: # (Policy)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1860)

# Policy

DevOps Secrets Vault permissions are foundational for proper operation and security.
Policies control access to resources and authorization to act on resources, such as to change them.

To get a json encoded list of all policies, use:

```bash
thy policy search
```

You can add a query item to search policies by path:

```bash
thy policy search secrets/databases
```

or

```bash
thy policy search –query secrets/databases
```

A typical policy looks like this:

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

| **Element**        | **Definition**                                                                                                                                                                                                                                                                                                                                          |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| actions            | a list of possible actions on the resource including create, read, update, delete, list, share, assign. (Regex and list supported)                                                                                                                                                                                                                      |
| conditions         | an optional CIDR range to lock down access to a specific IP range                                                                                                                                                                                                                                                                                       |
| description        | human friendly description of the policy intent                                                                                                                                                                                                                                                                                                         |
| effect             | whether the policy is allowing or preventing access; valid values are allow and deny                                                                                                                                                                                                                                                                    |
| id                 | system-generated unique identifier to track changes to a particular policy                                                                                                                                                                                                                                                                              |
| resources subjects | the resource path defining the targets to which the permissions apply; a resource path prefixes the entity type (secrets, clients, roles, users, config, config:auth, config:policies, audit, system:log) to a colon delimited path to the resource. users or entities to which the policy enables authorization. Prefixes include users, roles, groups |

## Policy Evaluation

To correctly evaluate permission policies, you must know the rules that apply to permissions.

* Permissions are cumulative.

  If there is a top level permission for the path secrets:servers:<.*> that grants a user write access, then even if they are only granted read access at the resource path secrets:servers:webservers:<.*>, they will still have write access due to the top level implicit match.

* An explicit deny trumps an explicit or implicit allow.

* Actions are explicit. A user assigned update and read will not automatically have create for the resource path.

* The list action has a special behavior.

  * First, list (search) is global—it runs across all items of an entity, not limited to paths and sub-paths.

  * Second, to grant a user an ability to search entities via `list`, use the root of the entity if you want `list` to include other entities and actions within the same policy. The root entity, for example, is secrets, with no other characters following.

## Policy Examples

### Deny Access at a Lower Level

**Case:** Subjects need access to secrets for an environment, but that logical environment contains a more restricted area.

**Solution:** Two policies. The first provides the Subjects (`developer1@thycotic.com|developer2@thycotic.com`) general access to the secrets resources at the path `secrets:servers:us-east-1:<.*>`.

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

The second policy adds an explicit `deny` with a more specific path to deny access at the more privileged level, as in the following example.

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

**Case:** A user needs to assign roles when they create client credentials, but must not be able to self-elevate by assigning an admin level role.

**Solution:** Use a naming convention when creating roles and specify a prefix with a wildcard to only allow users to assign roles that match the naming convention, as modeled in the following example.

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

**Case:** A user needs to read and list entities within a policy.

**Solution:** Add a list action and the root of the entity used for searching.

In the example below, `roles` is the entity for reading and searching (list action). In the **resources** section, `roles:dev-role-<.*>` is used for reading, while `roles` is used for searching.

```yAML
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

Now the developers can create policies below the `secrets:servers:` path; for example, developer1 can create policies for `secrets:servers:webservers` and developer2 can do the same at `secrets:servers:databases`.

![Article End](../dsv-bug.png)
