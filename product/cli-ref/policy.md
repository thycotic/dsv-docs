[title]: # (Policy)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (4700)

# Policy

Policies control access to resources and authorization to act on resources, such as to change them, via **permissions**.
DevOps Secrets Vault permissions are foundational for proper operation and security.

To get a json encoded list of all Policies, use:

`thy policy search`

You can add a query item to search Policies by path:

`thy policy search secrets/database` or `thy policy search --query secrets/databases`

A typical Policy looks like this:

```yaml
created: '2019-09-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2019-09-24T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: secrets:servers:us-west
permissionDocument:
- actions:
  - read
  conditions: {}
  description: ''
  effect: allow
  id: xxxxxxxxxxxxxxxxxxxx
  meta: 
  resources:
  - secrets:servers:us-west:<.*>
  subjects:
  - groups:west admins
version: '5'
```

![](./images/spacer.png)

A policy contains a list of permissions which define access to resource paths. The policy itself has a top level path which is the identifier of the policy as well. The policy path is used to validate the resource paths in the permission documents. This allows administrators to delegate user ownership of policies without allowing self elevation through modifying the policy to a higher level path.

For example, the policy above has a path of `secrets:servers:us-west`. Permissions can be created for resources paths like `secrets:servers:us-west`, `secrets:servers:us-west:<.*>`, or `secrets:servers:us-west:prod:<.*>`. A permission document cannot be created on the policy to allow users to manage users, i.e. with a resource path of `users:<*>`. Because the policy path must be the root of any resource paths in its permission documents.

The one exception is policy delegation. An admin can create a policy and add a resource path for `config:policies:secrets:servers:us-west` to allow users to manage the policy. An example of this is [below](###delegate-policy-authority) 

The permission document has the following elements:

| **Element**        | **Definition**                                                                                                                                                                                                                                          |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| actions            | a list of possible actions on the resource including create, read, update, delete, list, and assign (regular expressions and list supported)                                                                                                     |
| conditions         | an optional CIDR range to lock down access to a specific IP range                                                                                                                                                                                       |
| description        | human friendly description of the Policy intent                                                                                                                                                                                                         |
| effect             | whether the Policy is allowing or preventing access; valid values are allow and deny                                                                                                                                                                    |
| id                 | system-generated unique identifier to track changes to a particular Policy                                                                                                                                                                              |
| resources          | the resource path defining the targets to which the permissions apply; a resource path prefixes the entity type (secrets, clients, roles, users, config, config:auth, config:policies, audit, system:log) to a colon delimited path to the resource.    |
| subjects           | the Policy provides authorization to these entiries.  Includes Users, Roles, and Groups                                                                                                                                                       |

![](./images/spacer.png)

## Policy Evaluation

To correctly evaluate permission Policies, you must know the rules that apply to permissions.

* Values for permission properties may optionally be specified using a regular expression enclosed in angle brackets `<>`. For example,
a subject entry could be written as `["users:<bob|alice>"]`. Here, users `bob` and `alice` are specified. A longer alternative would be
`["users": "bob", "users": "alice"]`.
* Permissions are cumulative.
  * If there is a top level permission for the path secrets:servers:<.\*> that grants a User **write** access, then even if they are only granted **read** access at the resource path secrets:servers:webservers:<.\*>, they will still have write access due to the top level implicit match.
* `effect` can either be `allow` or `deny`. If not specified, it defaults to `allow`
* An explicit deny trumps an explicit or implicit allow.
* At least one action must be listed in an array. Actions are explicit. A User assigned **update** and **read** will not automatically have **create** for the resource path.
* For actions, the wildcard form `<.*>` replaces any other values, since it is an all-inclusive form.  A wildcard could be written as a standard `<.*>` form, but also as `.*` or `*` for convenience. The backend automatically converts it to `<.*>`.
* Invalid actions are not allowed, unless there is a wildcard element. Valid actions are `create, read, update, delete, assign, list`.
* The **list** action has a special behavior.
  * First, **list** (search) is global—it runs across all items of an entity (any of the resources like Useres, Roles, Groups, etc), not limited to paths and sub-paths.
  * Second, to grant a User an ability to search entities via *list*, use the root of the entity if you want *list* to include other entities and actions within the same Policy. The root entity, for example, is secrets, with no other characters following.
  * See the example on Search
* At least one subject must be listed in an array. A prefix is required. For example, a valid subject is `"users:bob"`. Valid prefixes are `groups, roles, users`.
* Subjects and actions are automatically converted to lower case upon save.

## Policy Examples

When creating or updating a Policy, a workflow can be started using `thy policy create` or `thy policy update` without flags.  This will start step-by-step quesitons to guide you though the process.  However, in the following examples, the direct command will be shown.


### Deny Access at a Lower Level

**Case:** Subjects need access to Secrets for an environment, but that logical environment contains a more restricted area.

**Solution:** Two Policies. The first provides the Subjects (*developer1@thycotic.com|developer2@thycotic.com*) general access to the Secrets resources at the path *secrets:servers:us-east-1:<.*>*.

The direct command to create this policy is `thy policy create --path secrets:servers:us-east-1 --actions '<.*>' --desc 'Developer Policy' --subjects 'users:<developer1@thycotic.com|developer2@thycotic.com>' --effect allow`  With the trickiest part being to remember the "secrets" prefix on the path.

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
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

The second Policy adds a specific path at a level lower (*secrets:servers:us-east-1:production*) to explicitly *deny* access to *developer1@thycotic.com*, as in the following example.

The command to create this policy is ``thy policy create --path secrets:servers:us-east-1:production --actions '<.*>' --desc 'Developer Deny Policy' --subjects 'users:<developer1@thycotic.com>' --effect deny`

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: secrets:servers:us-east-1:production
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

The command to run this is `thy policy create roles:dev-role --subjects users:developer@thycotic.com,roles:onboarding-role --desc 'Role Assignment' --resources 'roles:dev-role-<.*>' --actions assign`

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: roles:dev-role
permissionDocument:
- id: xxxxxxxxxxxx
description: Limited Role Assignment Policy.
subjects:
- users:developer@thycotic.com
- roles:onboarding-role
actions:
- assign
effect: allow
resources:
- roles:dev-role-<.*>
```

### Enable a Group to search Secrets

**Case:** Allow a Group to search secrets

**Solution:** Under the Resource entity, Secrets, enable the Group named "admins".

The command to create this policy is `thy policy create secrets --subjects groups:admins --desc 'secret search' --resources secrets --actions list`

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: secrets
permissionDocument:
- actions:
  - list
  conditions: {}
  description: secret search
  effect: allow
  id: xxxxxxxxxxxx
  meta: null
  resources:
  - secrets
  subjects:
  - groups:admins
version: "0"
```
> Note: Searching secrets only enables the users to see the path, but not the actual data in the secret.  That would require Read access at the proper path.

### Allow Users to List Specific Entities

**Case:** A User needs to search across all items but only needs full read access on specific ones

**Solution:** Add a list action and the root of the entity used for searching.

In the example below, *roles* is the entity for reading and searching (list action). In the **resources** section, *roles:dev-role-<.*>* is used for reading, while *roles* is used for searching.

The command to create this policy is `thy policy create roles --subjects users:developer@thycotic.com,roles:onboarding-role --desc 'Role Searching' --resources 'roles:dev-role-<.*>,roles' --actions read,list`

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: roles
permissionDocument:
- actions:
  - read
  - list
  conditions: {}
  description: Role Searching
  effect: allow
  id: xxxxxxxxxxxx
  meta: null
  resources:
  - roles:dev-role-<.*>
  - roles
  subjects:
  - users:developer@thycotic.com
  - roles:onboarding-role
version: "0"
```

The syntax of the latter is important. In general, the root form of an entity has no * after the entity name, or anything besides the name.

### Delegate Policy Authority

**Case:** An admin wants to delegate control to various team leads at a sub-path.

**Solution:** Under Resources, add config:policies followed by the resource path.

The command to create this policy is `thy policy create secrets:servers --actions create,read,update,delete --resources 'secrets:servers:<.*>,config:policies:secrets:servers:<.*>' --subjects 'users:<developer1@thycotic.com|developer2@thycotic.com>'`

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: secrets:servers
permissionDocument:
- actions:
  - create
  - read
  - update
  - delete
  conditions: {}
  description: ""
  effect: allow
  id: xxxxxxxxxxxx
  meta: nullb 
  resources:
  - secrets:servers:<.*>
  - config:policies:secrets:servers:<.*>
  subjects:
  - users:<developer1@thycotic.com|developer2@thycotic.com>
version: "0"
```

Now the developers can create Policies below the *secrets:servers:* path; for example, developer1 can create Policies for *secrets:servers:webservers* and developer2 can do the same at *secrets:servers:databases*.

### Read Audits

**Case:** A user needs to be able to read audit records

**Solution:** Add a policy for the audit resource path

The command to create this policy is `thy policy create audit --actions list --resources audit --subjects users:developer1@thycotic.com` 

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: audit
permissionDocument:
- actions:
  - list
  conditions: {}
  description: ""
  effect: allow
  id: xxxxxxxxxxxx
  meta: null
  resources:
  - audit
  subjects:
  - users:developer1@thycotic.com
version: "0"
```

### Read System Logs

**Case:** A user needs to be able to read the application log messages

**Solution:** Add a policy for the system:log resource path

The command to create this policy is `thy policy create system:log --actions list --resources system:log --subjects users:developer1@thycotic.com` 

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: system:log
permissionDocument:
- actions:
  - list
  conditions: {}
  description: ""
  effect: allow
  id: xxxxxxxxxxxx
  meta: null
  resources:
  - system:log
  subjects:
  - users:developer1@thycotic.com
version: "0"
```

### Manage An Auth Provider

**Case:** A user needs to update a single auth provider

**Solution:** Add a policy for the config:auth provider path

The command to create this policy is `thy policy create config:auth:gcp-dev --actions read,update --resources config:auth:gcp-dev --subjects users:developer1@thycotic.com` 

```yaml
created: '2020-06-24T18:12:26Z'
createdBy: users:thy-one:admin@company.com
id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
lastModified: '2020-07-16T20:13:53Z'
lastModifiedBy: users:thy-one:admin@company.com
path: config:auth:gcp-dev
permissionDocument:
- actions:
  - read
  - update
  conditions: {}
  description: ""
  effect: allow
  id: xxxxxxxxxxxx
  meta: null
  resources:
  - config:auth:gcp-dev
  subjects:
  - users:developer1@thycotic.com
version: "0"
```

![](./images/spacer.png)

![](./images/spacer.png)
