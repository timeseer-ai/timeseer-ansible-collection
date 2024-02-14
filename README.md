# timeseer-ansible-collection

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_ansible_collection-yellow.svg)](https://galaxy.ansible.com/diogolobo122/timeseer)

timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community.podman` Ansible collection.

## Example Playbook

```yaml
- hosts: your_target_hosts
  tasks:
    - name: Run Timeseer container
      ansible.builtin.import_role:
        name: timeseer.podman.timeseer

    - name: Run Timeseer reverse proxy container
      ansible.builtin.import_role:
        name: timeseer.podman.timeseer_reverse_proxy
      when: timeseer_reverse_proxy_enable is defined and timeseer_reverse_proxy_enable

    - name: Run Traefik reverse proxy
      ansible.builtin.import_role:
        name: timeseer.podman.traefik
      vars:
        traefik_letsencrypt_mail: "it-support@timeseer.ai"
        traefik_basic_auth_users: "{{ tsai_traefik_users + customer_traefik_users }}"
        traefik_expose_flight: true
```

## Installation

### Installing timeseer.podman Collection with Ansible

To install the timeseer.podman Ansible collection, use the `ansible-galaxy` tool included with Ansible.

#### Default Installation from Ansible Galaxy

Install the latest version with the following command:

```bash
$ ansible-galaxy collection install timeseer.podman
```

## Installing a Specific Version

To install a specific version, append the version number:

```bash
$ ansible-galaxy collection install timeseer.podman:1.0.0
```

## Overwriting Existing Collections

Use --force to overwrite any existing collections:

```bash
$ ansible-galaxy collection install --force timeseer.podman:1.0.0
```

More information can be found in the [official Ansible documentation](https://docs.ansible.com/).
