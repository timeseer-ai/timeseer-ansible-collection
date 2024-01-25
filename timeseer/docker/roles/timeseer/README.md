---

# Ansible Role: timeseer

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer-yellow.svg)](https://galaxy.ansible.com/ui/repo/published/diogolobo122/timeseer/content/role/timeseer/)


This Ansible role is designed for setting up timeseer, a time-series data analysis platform, particularly in a Dockerized environment. It handles storage directory preparations, Docker volume configurations, and manages the deployment of timeseer in a Docker container.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Variables

Variables used in this role are listed below, along with default values (see `defaults/main.yml`):

- `timeseer_dir`: Base directory for Timeseer data and configurations.
- `timeseer_docker_volume` (default: ''): Optional Docker volume for Timeseer storage.
- `timeseer_network`: Name of the Docker network for Timeseer.
- `timeseer_image`: Docker image for Timeseer.
- `timeseer_download_demo_data` (default: false): Whether to download demo data for Timeseer.
- `timeseer_config_dir` (default: ''): Optional directory for custom Timeseer configurations.

## Dependencies

No other Galaxy roles are dependencies for this role. However, it's important to ensure that Docker is properly installed and configured on your hosts.

## Example Playbook

Include this role in your playbooks as follows:

```yaml
- hosts: 
  roles:
     - timeseer.docker.timeseer
```

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer.

---
