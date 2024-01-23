---

# Ansible Role: traefik

This Ansible role is designed for deploying and configuring Traefik, a modern HTTP reverse proxy and load balancer, with a focus on integrating Let's Encrypt for SSL/TLS certificates. It includes tasks for checking prerequisites, preparing directories, templating configuration files, and running Traefik in a Docker container.

## Build Status Ansible Galaxy

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer-yellow.svg)](https://galaxy.ansible.com/user/DiogoLobo122)

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Variables

This role uses several variables, a few of which are listed below. For complete details, see `defaults/main.yml`:

- `traefik_letsencrypt_mail`: Email address used for Let's Encrypt notifications. This is a mandatory variable.
- `traefik_dir`: Base directory for Traefik configurations and certificates.
- `traefik_image`: Docker image for Traefik.
- `traefik_network`: Docker network for Traefik.
- `traefik_journal_tag`: Tag for journal logging.
- `traefik_recreate_container` (default: false): Flag to determine if the Traefik container should be recreated on playbook runs.

## Dependencies

There are no external role dependencies for this role. Ensure Docker is properly installed and configured on the target hosts.

## Example Playbook

Here's an example of how to include this role in a playbook:

```yaml
- hosts: timeseer-server
  roles:
    - traefi
```

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by [Your Organization/Your Name].

---

This README provides a basic outline for your Traefik setup role. You can adjust the content to better fit the specifics of your role and the conventions of your project or organization.
