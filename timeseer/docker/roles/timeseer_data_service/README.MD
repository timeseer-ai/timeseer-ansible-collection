---

# Ansible Role: timeseer_data_service

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_data_service-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

This Ansible role is designed to deploy and configure the Timeseer Data Service in a Docker environment. It handles tasks such as setting up the necessary storage directories, configuring Docker volumes, managing Docker containers for the Timeseer Data Service, and optionally configuring the service via TOML files.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.
## Role Overview

The Timeseer data service is an optional component specifically designed to enhance performance for data services in fleet scenarios. This service operates independently and has its own dedicated configuration file.


**Default Configuration:**
- **Configuration File:** `Timeseer-data-service.toml`
- You can specify an alternative configuration file location using the `timeseer_data_service_config_dir` variable.

**Service Setup:**
- To use this service, add the following settings to your Timeseer `timeseer_config_dir` as a  TOML file:
```toml
[data-service]
url = "http://timeseer-data-service:3000"
```
- This service is configured by default to use port `3000:3000`. To change this setting, you can modify the port mappings using Docker CLI syntax, for example, `"3003:3000"` which maps host port 3003 to container port 3000.

**Port Configuration:**
```yaml
timeseer_data_service_ports:
  - "3003:3000"
```

**Upgrading Data Service:**
- To upgrade the data service to version 3, navigate to **Resources > Manage Resources > Export Resources** on the Timeseer application page.
- Export the data service you wish to use with the remote data service and update its version to `version: 3`.
- Note: The default version is 1.

**Importing the Role:**
- To install the Timeseer data service, use the following YAML configuration:
```yaml
- name: install timeseer data-service
  ansible.builtin.import_role:
    name: "timeseer.docker.timeseer_data_service"
```
## Role Variables

Variables used in this role are listed below, along with default values (see `defaults/main.yml`):

- `timeseer_data_service_dir` (default: `"/opt/timeseer/data-service"`): The directory for the Timeseer Data Service.
- `timeseer_data_service_docker_volume` (default:` ''`): Optional Docker volume for Timeseer data service storage.
- `timeseer_data_service_config_dir` (default:` ""`): Optional directory for TOML configuration files.
- `timeseer_data_service_enable` (default: `false`): Boolean to enable or disable the data service.
- `timeseer_data_service_image` (default: `"container.timeseer.ai/timeseer"`): docker image used for the Timeseer Data Service.
- `timeseer_data_service_ports` (default: `[]`): Port mappings for the service. Modify using Docker CLI syntax, such as
     - "3003:3000" (maps host port 3003 to container port 3000)
     - "0.0.0.0:3003:3000" (binds host port 3003 on all interfaces to container port 3000)
- `timeseer_data_service_network` (default: `"timeseer"`): docker network to which the service connects.
- `timeseer_data_service_journal_tag` (default: `"TIMESEER-DS"`): Tag used for logging purposes.
- `timeseer_data_service_image_pull` (default: `true`): Whether to pull the latest docker image.

## Dependencies

There are no external role dependencies for this role, but Docker must be installed and operational on the target hosts.

## License

Refer to the `LICENSE.md` file in this repository for licensing information.

## Author Information

This role was created in 2024 by Timeseer.

---
