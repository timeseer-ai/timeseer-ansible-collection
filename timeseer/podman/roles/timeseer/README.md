---

# Ansible Role: timeseer

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer-yellow.svg)](https://galaxy.ansible.com/ui/repo/published/diogolobo122/timeseer/content/role/timeseer/)


This Ansible role is designed for setting up timeseer, a time-series data analysis platform, particularly in a podmanized environment. It handles storage directory preparations, podman volume configurations, and manages the deployment of timeseer in a podman container.

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community.podman` Ansible collection.

## Role Variables

Variables used in this role are listed below, along with default values (see `defaults/main.yml`):

- `timeseer_dir`: Base directory for Timeseer data and configurations.
- `timeseer_podman_volume` (default: ''): Optional podman volume for Timeseer storage.
- `timeseer_network`: Name of the podman network for Timeseer.
- `timeseer_image`: podman image for Timeseer.
- `timeseer_download_demo_data` (default: false): Whether to download demo data for Timeseer.
- `timeseer_config_dir` (default: ''): Optional directory for custom Timeseer configurations.
- `timeseer_ports:` (default: ''): Custom port mapping for the Timeseer container. This should be specified in the podman port mapping format (e.g., '8080:8080') 

## Dependencies

No other Galaxy roles are dependencies for this role. However, it's important to ensure that podman is properly installed and configured on your hosts.

## Example Playbook

Include this role in your playbooks as follows:

```yaml
- hosts: 
  import_role:
     - timeseer.podman.timeseer
```

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer.

---
