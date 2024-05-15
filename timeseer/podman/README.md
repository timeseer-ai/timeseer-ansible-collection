# timeseer podman Roles

## Introduction

This repository contains the Ansible roles for configuring and managing the timeseer environment within podman. Each role is specifically tailored to streamline various aspects of the timeseer setup and deployment.

## Roles Overview

- **[timeseer]**: Sets up Timeseer in podman, handling storage preparation, podman volumes, and deployment.
- **[timeseer_reverse_proxy]**: Configures Timeseer and its reverse proxy in podman, including storage, podman containers, and proxy
  setup.
- **[timeseer_data_service]**: Configures Timeseer data service for Podman, enhancing performance for data services in fleet scenarios
- **[timeseer_traefik]**: Deploys Traefik as a reverse proxy and load balancer in podman, focusing on SSL/TLS with Let's Encrypt.

## Usage

To use these roles, follow the instructions in each role's directory. Ensure you understand the role's purpose and requirements before implementation.

## Contributions

Contributions to improve or extend the roles are welcome. Please follow the contribution guidelines outlined in the repository.

## License

This collection is licensed under MIT. See the LICENSE file for more details.

## More Information

For detailed information and specific configuration instructions, please visit [Timeseer.AI Ansible Collection](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles).
