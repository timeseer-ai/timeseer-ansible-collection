---

# Ansible Role: timeseer_reverse_proxy

 [![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_reverse_proxy-yellow.svg)]

This Ansible role facilitates the setup and configuration of Timeseer and its reverse proxy in a Docker environment. The role includes tasks for preparing storage directories, configuring Docker volumes, managing Docker containers for Timeseer, and setting up a reverse proxy for Timeseer.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Variables

The role utilizes several variables, a few of which are outlined below. For a full list, refer to `defaults/main.yml`:

- `timeseer_reverse_proxy_dir`: Directory for the reverse proxy configuration.
- `timeseer_reverse_proxy_image`: Docker image for the Timeseer reverse proxy.
- `timeseer_reverse_proxy_config_dir` (default: ''): Optional directory for custom reverse proxy configurations.
- `timeseer_reverse_proxy_journal_tag`: Tag for journal logging.

## Dependencies

No additional roles are required. Ensure Docker is properly installed and configured on the target hosts.

## Example Playbook

Including an example of how to use your role:

```yaml
- hosts: timeseer-server
  roles:
    - timeseer_reverse_proxy
```

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer.

---
