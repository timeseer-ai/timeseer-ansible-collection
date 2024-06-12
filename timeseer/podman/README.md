# timeseer podman Roles

## Introduction

This repository contains the Ansible roles for configuring and managing the timeseer environment within podman. Each role is specifically tailored to streamline various aspects of the timeseer setup and deployment.

## Roles

- **timeseer**: Sets up Timeseer in podman, handling storage preparation,podman volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/podman-timeseer-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles/timeseer)

- **timeseer_reverse_proxy**: Configures Timeseer and its reverse proxy in podman, including storage, podman containers, and proxy setup.  
  [![Ansible Galaxy](https://img.shields.io/badge/podman-timeseer_reverse_proxy-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles/timeseer_reverse_proxy)

- **timeseer_traefik**: Deploys Traefik as a reverse proxy and load balancer in podman, focusing on SSL/TLS with Let's Encrypt.  
  [![Ansible Galaxy](https://img.shields.io/badge/podman-timeseer_traefik-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles/traefik)

- **timeseer_data_service**: Deploys timeseer_data_service ,handling storage preparation,podman volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/podman-timeseer_data_service-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles/timeseer_data_service)

---

## Usage

To use these roles, follow the instructions in each role's directory. Ensure you understand the role's purpose and requirements before implementation.

## Contributions

Contributions to improve or extend the roles are welcome. Please follow the contribution guidelines outlined in the repository.

## License

This collection is licensed under MIT. See the LICENSE file for more details.

## More Information

For detailed information and specific configuration instructions, please visit [Timeseer.AI Ansible Collection](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/podman/roles).
