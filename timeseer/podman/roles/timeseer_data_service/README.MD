---

# Ansible Role: timeseer_data_service

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_data_service-blue.svg)](https://galaxy.ansible.com/ui/repo/published/diogolobo122/timeseer/content/role/timeseer_data_service/)

This Ansible role is designed to deploy and configure the Timeseer Data Service in a podman environment. It handles tasks such as setting up the necessary storage directories, configuring podman volumes, managing podman containers for the Timeseer Data Service, and optionally configuring the service via TOML files.

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community.podman` Ansible collection.

## Role Variables

Variables used in this role are listed below, along with default values (see `defaults/main.yml`):

- `timeseer_data_service_dir` (default: "/opt/timeseer/data-service"): The directory for the Timeseer Data Service.
- `timeseer_data_service_podman_volume` (default: ''): Optional Docker volume for Timeseer data service storage.
- `timeseer_data_service_toml_dir` (default: ""): Optional directory for TOML configuration files.
- `timeseer_data_service_enable` (default: false): Boolean to enable or disable the data service.
- `timeseer_data_service_image` (default: "container.timeseer.ai/timeseer"): Podman image used for the Timeseer Data Service.
- `timeseer_data_service_ports` (default: "3000:3000"): Port mappings for the service. Modify using Docker CLI syntax, such as "3003:3000", "0.0.0.0:3003:3000", where "3003" can be an example of a host port and "3000" is the container port.
- `timeseer_data_service_network` (default: "timeseer"): Podman network to which the service connects.
- `timeseer_data_service_journal_tag` (default: "TIMESEER-DS"): Tag used for logging purposes.
- `timeseer_data_service_image_pull` (default: true): Whether to pull the latest Podman image.


## Dependencies

There are no external role dependencies for this role, but podman must be installed and operational on the target hosts.

## Example Playbook

Here's an example of how to include this role in your playbook:

```yaml
- hosts: timeseer-servers
  import_role:
    - timeseer.podman.timeseer_data_service
```

## License

Refer to the `LICENSE.md` file in this repository for licensing information.

## Author Information

This role was created in 2024 by Timeseer.

---
