[title]: # (Engine)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (6500)

# Engine

An engine is an agent performing tasks on any remote machine. After deployment, the agent opens a real-time two-way communication channel with the main DSV
API. Users of the API can send the agent tasks to complete, and the agent, having completed a task or failed, reports back to the caller.

One such task is, for example, creating a temporary MySQL account.
This involves reading a dynamic secret of type MySQL. Reading a previously created secret prompts the API to send a message describing
a task to engine. The task is to create a MySQL account on a given MySQL server (it can be located anywhere). As with dynamic secrets of other types,
the API creates the temporary credentials (username, password, etc.). With MySQL dynamic secrets, the API also sends them to engine. The running engine receives
them and creates a new MySQL account on the server. Assuming the engine succeeded, the engine reports back to the API, and the caller gets the new working credentials
in the API response.


An engine is designed to be a long-running process that completes tasks on demand and automatically in the background.

### Registering a pool and an engine

Users can create engines as other entities (like roles, users) in DSV. DSV organizes engines in pools, so an engine must be assigned to an existing pools.
Using the [DSV API](https://dsv.thycotic.com/api/index.html#tag/Pools), users first create a pool, then an engine assigned to that pool. An engine can only be assigned to one pool. A pool can contain many engines.


### Starting an engine

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
