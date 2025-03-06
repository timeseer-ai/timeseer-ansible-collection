# Ansible Role: timeseer_network

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_data_service-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

This role creates a Docker network that is used by the containers started by the `timeseer`, `timeseer_reverse_proxy` and `timeseer_data_service` roles.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Overview

This role is used automatically by the roles that define containers.

To include it manually:

```yaml
- name: Define a network for the Timeseer containers
  ansible.builtin.import_role:
    name: "timeseer.docker.timeseer_network"
```
## Role Variables

Variables used in this role are listed below, along with default values (see `defaults/main.yml`):

- `timeseer_network_name` (default: `"timeseer"`): docker network to which the containers will connect.

## Dependencies

There are no external role dependencies for this role, but Docker must be installed and operational on the target hosts.

## License

Refer to the `LICENSE.md` file in this repository for licensing information.

## Author Information

This role was created in 2025 by Timeseer.
