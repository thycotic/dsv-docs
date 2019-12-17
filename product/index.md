[title]: # (Getting Started)
[tags]: # (DevOps Secrets Vault,DSV,)
[priority]: # (1)

# Welcome to DevOps Secrets Vault (DSV) Technical Assistance

This December, DSV changed the `thy init` command used to initialize accounts so that it always steps through all key options, producing a more consistent experience for users and better aligning with typical command usage. The December release also countered a potential security risk identified in the audit log system, and added automatic update availability notification.

---

New user? Make sure you have done all your setup and configuration steps.

*If you have **already** signed up for a vault, installed the CLI executable, and configured for your authentication provider, you’re good to go. If you have not completed any of those steps, please note the following to get started.*

The DevOps Secrets Vault service operates through two main components: a Cloud-hosted, API-driven vault that stores your Secrets, and a locally installed executable that gives you a Command Line Interface (CLI) for managing your Secrets through the API. Neither works without the other, so you must set up both before you can use DevOps Secrets Vault. Additionally, once you are up and running, you must configure for your authentication provider.

The first several documents in this collection (as listed below, and in the Navigation Panel at left) will get you started.

---

# About This Collection

Thycotic created this collection of short-form technical materials to quickly and directly connect you with answers to your DSV questions. We aim to conserve your time by applying brevity and focus at every turn.

* When you have a specific topic in mind, the search tool at upper right will be your quickest path to relevant articles.
* For browsing, the Navigation Panel (menus at left) pulls together information supporting a solid grounding in DevOps Secrets Vault.

See the [Overview](./overview/index.md) for a quick product orientation.

Visit [Obtain DevOps Secrets Vault](./obtain/index.md) to learn how to sign up for your Thycotic One Cloud-hosted vault (the first component) and download and place the CLI executable (the second component).

[Setup DevOps Secrets Vault](./setup/index.md) explains initial setup steps. Authentication figures prominently:

* [Authentication: General](./authent-gen/index.md) covers typical installations.
* [Authentication: Azure or AWS](./authent-azure-aws/index.md) covers use of DSV with third party authentication platforms.

After you finish setup, you can learn more about DevOps Secrets Vault in the [CLI Primer](./cli-primer/index.md), [CLI Secrets Examples](./cli-examples/index.md), and [CLI Reference](./cli-ref/index.md) articles.

The [Release Notes](release-notes.md) alert you to any changes or other considerations related to service updates (releases), with the latest Release Notes listed first.

[Support Resources](./support/index.md) connects you to available product support.

[Quick Links](./quick-links/) lists for your convenience commonly used links related to DevOps Secrets Vault.

The **Related docs** button (located at upper left, at the top of the left-side Navigation Panel) provides access to articles about the [Jenkins](/dsv-extension-jenkins) and [Kubernetes](/dsv-extension-kubernetes) extensions and the [DSV Java SDK](/dsv-sdk-java).

Alongside this collection, Thycotic maintains a [DevOps Secrets Vault API Reference](https://dsv.thycotic.com/api).
