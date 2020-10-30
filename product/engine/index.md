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

To start a DSV Engine, perform the following actions. **Note:** the example uses the placeholders 'examplepool' and 'exampleengine', replace these with the correct engine and pool names for your organization.
|Guide|CLI|
|---|---|
|1. Create an **Engine pool**.| `dsv engine create --name examplepool`|
|2. Create an **Engine** and assign it to the pool. **Note**: An Engine can only be assigned to one pool.| `dsv engine create --name exampleengine --name examplepool`|
|3. Install the **DSV Engine Binaries**.| https://dsv.thycotic.com/downloads |
|4. **Register** the Engine. The private key can be retrieved using the `dsv whoami` command.| `dsv engine register --endpoint example.endpoint.com --engine-name exampleengine --private key exampleprivatekey`|
|5. (Optional) **Ping** the Engine to ensure connectivity.| `dsv engine ping --name exampleengine`|

<!--### Registering a pool and an engine

Users can create engines as other entities (like roles, users) in DSV. DSV organizes engines in pools, so an engine must be assigned to an existing pools.
Using the [DSV API](https://dsv.thycotic.com/api/index.html#tag/Pools), users first create a pool, then an engine assigned to that pool. An engine can only be assigned to one pool. A pool can contain many engines.-->


### Starting an Engine in a Container

To start an engine in a container, pull the appropriate image and run a container from it. The result will depend on the
environment variables you provide to the new container.
If you had created a pool, but not engine, you can register a new engine and start it in one step:

`docker run -e ENGINE_NAME=engine1 -e DSV_POOL=pool1 -e DSV_TENANT=bob -e DSV_URL=secretsvaultcloud.com -e DSV_TOKEN=eyJhbGcxNjAKadw dsv-engine`

You should see the private key and other information about the new engine displayed once it has been registered,
and the container has been started. Store the private key and other information securely.

If you already have a registered engine and want to run it in the container, then provide a different set of environment variables:

`docker run --name eng --rm -e ENGINE_NAME=engine1 -e DSV_ENDPOINT=bob.ws.secretsvaultcloud.com -e DSV_PRIVATE_KEY=LS0tLS1CRUiBSkFURS` dsv-engine

In either case, on successful engine start, you should a message saying that the engine is ready and waiting for messages.

##### List of environment variables for engine Docker container
- ENGINE_NAME
- DSV_POOL
- DSV_TENANT
- DSV_URL
- DSV_TOKEN
- DSV_PRIVATE_KEY
- DSV_ENDPOINT

#### Running the dsv-engine binary directly

The container encapsulates the operations of the dsv-engine binary, which is a client-side CLI program to register and run an engine.
It exposes two commands: `register` and `run`.
Standard help is available with `dsv-engine register -h` and `dsv-engine run -h`.
