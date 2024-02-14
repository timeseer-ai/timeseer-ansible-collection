---
# Ansible Role: timeseer_traefik

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_traefik-yellow.svg)](https://galaxy.ansible.com/user/timeseer_reverse_proxy)


This Ansible role is designed for deploying and configuring Traefik, a modern HTTP reverse proxy and load balancer, with a focus on integrating Let's Encrypt for SSL/TLS certificates. It includes tasks for checking prerequisites, preparing directories, templating configuration files, and running Traefik in a podman container.

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community.podman` Ansible collection.

## Role Variables

This role uses several variables, a few of which are listed below. For complete details, see `defaults/main.yml`:

- `traefik_letsencrypt_mail`: Email address used for Let's Encrypt notifications. This is a mandatory variable.
- `traefik_dir`: Base directory for Traefik configurations and certificates.
- `traefik_image`: podman image for Traefik.
- `traefik_network`: podman network for Traefik.
- `traefik_journal_tag`: Tag for journal logging.
- `traefik_recreate_container` (default: false): Flag to determine if the Traefik container should be recreated on playbook runs.

## Dependencies

There are no external role dependencies for this role. Ensure podman is properly installed and configured on the target hosts.

## Example Playbook

Here's an example of how to include this role in a playbook:

```yaml
- hosts: timeseer-server
  roles:
    - timeseer.podman.traefik
```

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer

---
