

  <a href="https://epicchain.org">EpicChain</a> Local Development and Testing Environment
</p>

---
## Overview

This repository provides comprehensive tools and configurations for setting up a local EpicChain network and private networks, collectively referred to as Devenv. Devenv serves as a development and testing environment that enables developers to interact with EpicChain components in a controlled, on-premise setup. This environment is crucial for ensuring that the various parts of the EpicChain ecosystem work seamlessly before deploying them to production.

## Prerequisites

Before you start, ensure that you have installed the following prerequisites on your development machine. These tools are essential for the proper functioning of the Devenv setup:

* **Docker**: A platform for developing, shipping, and running applications inside containers. It isolates applications in a consistent environment.
* **Docker Compose**: A tool for defining and running multi-container Docker applications. It allows you to manage multiple containers with a single configuration file.
* **Make (`3.82+`)**: A build automation tool that helps manage and automate the process of building and deploying software.
* **Expect**: A tool for automating interactive applications, useful for automating the process of interacting with scripts and programs that require user input.
* **OpenSSL**: A robust toolkit for the implementation of secure communications over networks, providing the necessary cryptographic functions.
* **jq**: A command-line tool for processing JSON data, allowing you to extract and manipulate JSON content.
* **base64 (coreutils)**: A utility for encoding and decoding base64 data, which is often used for data encoding in configurations and communications.

## Quick Start

Follow these steps to quickly set up and start using the EpicChain development environment:

1. **Clone the Repository**: Begin by cloning the repository to your local machine. This step retrieves the source code and configurations required for the Devenv setup.

    ```bash
    $ git clone https://github.com/epicchain-dev/devenv.git
    ```

2. **Initial Setup**: Run the following commands from the root directory of the cloned repository:

    ```bash
    $ make get
    ```

    This command is necessary for the first run to execute `make hosts`. This step will download and prepare the necessary files and configurations. If the hosts have already been set up, you can skip this step in subsequent runs.

    ```bash
    $ make hosts
    192.168.130.10 bastion.epicchain.devenv
    192.168.130.50 main-chain.epicchain.devenv
    192.168.130.61 ir01.epicchain.devenv
    ...
    192.168.130.74 s04.epicchain.devenv
    ```

    This command will display the addresses and hostnames of various components in the EpicChain environment. You need to add these entries to your local `/etc/hosts` file to ensure that your system can resolve these addresses correctly.

3. **Start All Services**: Launch all the necessary services using the following command. This will start the containers and configure the network as specified.

    ```bash
    $ make up
    ```

4. **Prepare Wallet and Certificates**: Once the services are up and running, you need to prepare a GAS deposit for the test wallet to cover EpicChain operations. The test wallet configuration can be found in `wallets/wallet.json`, and the wallet password is empty.

    ```bash
    $ make prepare.ir
    password >
    fa6ba62bffb04030d303dcc95bda7413e03aa3c7e6ca9c2f999d65db9ec9b82c
    ```

    Additionally, you must add the self-signed certificate from node (`s04.epicchain.devenv`) to your trusted store. This step is crucial for client services (like epicchain-rest-gw and epicchain-s3-gw) to securely interact with the node.

    ```bash
    $ make prepare.storage
    ```

5. **Update Configuration Values**: Use the `make update.*` commands to adjust the global configuration values of EpicChain. The inner ring wallet password is `one`. This allows you to modify various settings, such as the epoch duration.

    ```bash
    $ make update.epoch_duration val=30
    Changing EpochDuration configuration value to 30
    Enter account NNudMSGzEoktFzdYGYoNb3bzHzbmM1genF password >
    Sent invocation transaction dbb8c1145b6d10f150135630e13bb0dc282023163f5956c6945a60db0cb45cb0
    Updating EpicChain epoch to 2
    Enter account NNudMSGzEoktFzdYGYoNb3bzHzbmM1genF password >
    Sent invocation transaction 0e6eb5e190f36332e5e5f4e866c7e100826e285fd949e11c085e15224f343ba6
    ```

For instructions specific to setting up Devenv on macOS, refer to the guide located in the `docs` directory: [macOS Guide](docs/macOS.md).

## How It's Organized

The Devenv setup is organized as follows:

```
.
├── Makefile         # Contains commands to manage the Devenv environment
├── .services        # Lists the services to be used and their start order
├── services         # Defines service configurations and files
│   ├── basenet       # Base network service
│   ├── chain         # Main chain service
│   ├── ir            # Inner ring service
│   └── storage       # Storage service
├── vendor           # Temporary files and artifacts
└── wallets          # Wallet files for managing GAS assets
```

The `Makefile` contains the main commands and targets to manage Devenv's services. Each service is defined in its respective directory under `services/`, including all required files and scripts.

The list of services and their startup order is specified in the `.services` file. You can customize this file to include or exclude services based on your needs.

Additional information on each service can be found in the `docs` directory. If you have any questions, you may find answers in the [F.A.Q.](docs/faq.md).

## Using EpicChain Admin Tool in `dev-env`

Devenv supports management of the EpicChain network via the [epicchain-adm](https://github.com/epicchain-dev/epicchain-node/tree/master/cmd/epicchain-adm) tool. The `services/ir` directory includes the Alphabet wallet in the required format; specify it using the `--alphabet-wallets` flag.

## Notable Make Targets

The `make help` command will provide a brief description of available targets. Here are some notable targets:

### up

This target starts all Devenv services. It performs the following steps:
- `pull`: Retrieves the latest container images.
- `get`: Downloads necessary artifacts.
- `vendor/hosts`: Generates the hosts file.
- Starts all services in the order specified in the `.services` file.

### down

This target shuts down all services. It will destroy all containers and networks created during the setup. Any changes made within containers will be lost.

### hosts

Displays the addresses and host names for each running service, if available.

### clean

Cleans up the `vendor` directory and removes Docker volumes associated with services, including:
- Stored EpicChain objects
- EpicChain chain state

## Contributing

We welcome contributions to this project. Please review the [contributing guidelines](CONTRIBUTING.md) before you start. Create a new issue to discuss any feature or topic you plan to work on before you begin implementation.

# License

- [GNU General Public License v3.0](LICENSE)
