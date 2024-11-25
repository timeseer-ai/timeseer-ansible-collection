---
# Ansible Role: timeseer_traefik

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_traefik-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

This Ansible role is designed for deploying and configuring Traefik, a modern HTTP reverse proxy and load balancer, with a focus on integrating Let's Encrypt for SSL/TLS certificates. It includes tasks for checking prerequisites, preparing directories, basic authentication, templating configuration files, and running Traefik in a Docker container.

## Requirements

- Ansible 2.9 or higher.
- Docker installed on the target machine.
- `community.docker` Ansible collection.

## Role Overview
Traefik supports generating SSL certificates using [Let's Encrypt](https://letsencrypt.org/).

The role allows configuring Basic Authentication through Traefik,

providing a convenient way to handle authentication in PoC setups.

Note that Timeseer does not provide support for Traefik.

This role is provided for convenience.

Import the role using the following YAML:
```yaml
- name: Import Timeseer Docker role  Traefik
  ansible.builtin.import_role:
    name: "timeseer.docker.traefik"
```
## Role Variables

Below are the variables used in this role, along with their default values specified in `defaults/main.yml`:

- `traefik_letsencrypt_mail`: Email address for Let's Encrypt notifications. (default: `""`)
- `traefik_dir`: Base directory for Traefik configurations and certificates. (default: `"/opt/timeseer/traefik"`)
- `traefik_image`: docker image for Traefik. (default: `"traefik:v2.10"`)
- `traefik_network`: docker network for Traefik. (default: `"timeseer"`)
- `traefik_journal_tag`: Tag for journal logging. (default: `"TRAEFIK"`)
- `traefik_recreate_container`: Flag to determine if the Traefik container should be recreated on playbook runs. (default: `false`)
- `traefik_host`: Hostname for the Traefik service. (default: `"localhost"`)
- `traefik_basic_auth_enable`: Set to `true` to configure basic authentication. (default: `false`)
- `traefik_basic_auth_users`: List of users for basic authentication. Each entry should be in the format `"username:hashedpassword"`. (default: `[]`)
- `traefik_expose_flight`: Whether to expose the flight interface on Traefik. (default: `false`)
- `traefik_timeseer_reverse_proxy_enable`: Whether to enable the Timeseer reverse proxy configuration in Traefik. (default: `false`)
- `traefik_tls_enable`: Set to `false` to disable TLS completely. (default: `true`)
- `traefik_tls`: Method for obtaining SSL/TLS certificates. Use `"acme"` to automatically obtain certificates using the ACME protocol or `"certs"` to use your own pre-existing certificates. (default: `"acme"`)
- `traefik_cert_file_name`: Specifies the file path for your SSL/TLS certificate when using your own certificates. (default: `""`)
- `traefik_key_file_name`: Specifies the file path for your private key when using your own SSL/TLS certificates. (default: `""`)
---

## Dependencies

There are no external role dependencies for this role. Ensure Docker is properly installed and configured on the target hosts.

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer

---
