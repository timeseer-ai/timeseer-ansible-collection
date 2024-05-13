---

# Ansible Role: timeseer_data_service

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_data_service-blue.svg)](https://galaxy.ansible.com/ui/repo/published/diogolobo122/timeseer/content/role/timeseer_data_service/)

This Ansible role is designed to deploy and configure the Timeseer Data Service in a Docker environment. It handles tasks such as setting up the necessary storage directories, configuring Docker volumes, managing Docker containers for the Timeseer Data Service, and optionally configuring the service via TOML files.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Variables

Several variables are available for configuration within this role. Key variables include:

- `timeseer_data_service_dir`: The directory for the Timeseer Data Service.
- `timeseer_data_service_toml_dir`: Optional directory for TOML configuration files.
- `timeseer_data_service_enable`: Boolean to enable or disable the data service.
- `timeseer_data_service_image`: Docker image used for the Timeseer Data Service.
- `timeseer_data_service_ports`: Port mappings for the service, formatted as "host:container".
- `timeseer_data_service_network`: Docker network to which the service connects.
- `timeseer_data_service_journal_tag`: Tag used for logging purposes.
- `timeseer_data_service_image_pull`: Whether to pull the latest Docker image.

For a comprehensive list of all configurable variables, refer to the `defaults/main.yml` file in this role.

## Dependencies

There are no external role dependencies for this role, but Docker must be installed and operational on the target hosts.

## Example Playbook

Here's an example of how to include this role in your playbook:

```yaml
- hosts: timeseer-servers
  roles:
    - timeseer.docker.timeseer_data_service
```

## License

Refer to the `LICENSE.md` file in this repository for licensing information.

## Author Information

This role was created in 2024 by Timeseer.

---
