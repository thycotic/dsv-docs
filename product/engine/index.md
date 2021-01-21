[title]: # (DSV Engine)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6500)

# DSV Engine

An engine is an agent performing tasks on any remote machine. After deployment, the agent opens a real-time two-way communication channel with the main DSV
API. Users of the API can send the agent tasks to complete, and the agent, having completed a task or failed, reports back to the caller.

An engine is designed to be a long-running process that completes tasks on demand and automatically in the background.

The initial use of the DSV Engine will be to support database dynamic secrets.  In this use-case, a user or application will request access to a database.  DSV will have a "base" secret that gives DSV access to the database and permission to create users along with permissions and credentials.  DSV will provide those new credentials to the user or application for use.  Then when the TTL expires, DSV will go back to the database and delete that user.  This provides just-in-time access and eliminates the need for credential rotation.

Future uses of the DSV Engine will include additional authentication methods and password rotation.

## Organization Firewall 
The DSV Engine uses secure websockets (wss) on port 443 TCP outbound.  Since most organizations will already have this port open for web access, you will likely not need to make firewall changes.

## Starting an Engine

There are three methods for creating and starting an engine:

* Using the **DSV-Engine Program** and the **wizard**. This option simplifies engine creation into workflows.
* Using the **DSV-Engine Program** and **flags**. This option allows manual input of flags to register and run an engine.
* Using the CLI and DSV-Engine Program **separately**. This option allows creation of an engine and engine pool in the CLI before running the engine using the dsv-engine program.

>**NOTES:** 
> 1. The first time an engine is created, a matching configuration file called **.dsv-engine-config.yml** will also be created in your home directory. The `dsv-engine run` command will automatically use the values in this file unless another configuration is specified. You can create multiple configuration files and use them by specifying the path along with the run command (ie: `dsv-engine run --dsv-engine-config2.yml`).
> 1. Setting up the Engine with **Oracle** Databases has separate requirements. See the [Oracle](../dynamic-secrets/databases/oracle.md) page for instructions.

### Engine Wizard

|Guide|CLI|
|---|---|
|1. Download the **dsv-engine program** for your operating system. The example uses **dsv-engine** as the program name. | https://dsv.thycotic.com/downloads|
|2. Begin the **registration wizard** and follow the prompts in the CLI to register the engine. <br> **NOTE**: The user-token is your authorization token found using the `dsv auth` command in the DSV CLI.| `dsv-engine register --wizard`|
|3. Start the engine using `dsv-engine run` or the **run wizard**.| `dsv-engine run` **OR** `dsv-engine run --wizard`|

### Engine Flags 

|Guide|CLI|
|---|---|
|1. Download the **dsv-engine program** for your operating system. The example uses **dsv-engine** as the program name. | https://dsv.thycotic.com/downloads|
|2. Use the **register** command followed by the required flags to register the Engine. <br> **Flags:** <br> **--engine-name**: The name of the new engine.<br>**--pool-name**: The name of the new pool. If you omit pool-name, the engine will generate a random name for the engine pool.<br>**--endpoint:** The location of the engine. Use your tenant name followed by the domain you wish to use.<br>**--user-token:** The authorization token from the **dsv CLI**. Use the command `dsv auth` to retrieve the token.| `dsv-engine register --endpoint <tenantname>.secretsvaultcloud.com` `--engine-name <exampleengine> --user-token <exampletoken>`|
|3. Use the **run** command to start the engine.| `dsv-engine run`|

### CLI & Engine Program 

To start a DSV Engine, perform the following actions. The example uses the placeholders 'examplepool' and 'exampleengine', replace these with the correct engine and pool names for your organization.


|Guide|CLI|
|-------------------------|------|
|1. Create an **Engine pool**.| `dsv pool create --name examplepool`|
|2. Create an **Engine** and assign it to the pool. **Notes:** The create command will return a private key and endpoint. **Make sure to save the private key for Engine registration. It cannot be retrieved later.** An Engine can only be assigned to one pool.| `dsv engine create --name exampleengine --pool-name examplepool`|
|3. Install the **dsv-engine program**. The example uses **dsv-engine** as the program name. *If you use the same name, make sure to include the dash when performing registration in step 4.*| https://dsv.thycotic.com/downloads|
|4. **Run** the Engine.| `dsv-engine run --endpoint <tenantname>.<secretsvaultcloud.com>` `--engine-name exampleengine --private-key exampleprivatekey`|
|5. (Optional) **Ping** the Engine to ensure connectivity.| `dsv engine ping --name exampleengine`|
|6. (Optional) For support using the Engine Binary, use the built-in CLI help commands.| `dsv-engine register -h` and `dsv-engine run -h`|

<br>

<!--### Registering a pool and an engine

Users can create engines as other entities (like roles, users) in DSV. DSV organizes engines in pools, so an engine must be assigned to an existing pools.
Using the [DSV API](https://dsv.thycotic.com/api/index.html#tag/Pools), users first create a pool, then an engine assigned to that pool. An engine can only be assigned to one pool. A pool can contain many engines.-->

## Starting an Engine in a Container

To start an engine in a container, pull the appropriate image and run a container from it. The result will depend on the
environment variables you provide to the new container.
If you had created a pool, but not engine, you can register a new engine and start it in one step:
> **Note:** DSV_TOKEN is used to authenticate into the API. It can be generated in the CLI with `dsv auth`.

```CLI
docker run -e DSV_ENGINE=exampleengine -e DSV_POOL=examplepool -e DSV_ENDPOINT=<tenant.secretsvaultcloud.com> -e DSV_TOKEN=<tokentext> dsv-engine
```

You should see the private key and other information about the new engine displayed once it has been registered,
and the container has been started. Store the private key and other information securely.

If you already have a registered engine and want to run it in the container, then provide a different set of environment variables:

```CLI
docker run --name eng --rm -e DSV_ENGINE=exampleengine -e DSV_ENDPOINT=<tenantname>.secretsvaultcloud.com -e DSV_PRIVATE_KEY=<privatekey> dsv-engine
```

On a successful engine start, you should receive a response saying that the engine is ready and waiting for messages.

##### List of environment variables for engine Docker container
- ENGINE_NAME
- DSV_POOL
- DSV_TOKEN
- DSV_PRIVATE_KEY
- DSV_ENDPOINT
- DSV_VERBOSITY (WARN,DEBUG,ERROR,INFO)