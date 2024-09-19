# timeseer-ansible-collection

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_ansible_collection-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Requirements

- Ansible 2.9 or higher.
- docker installed on the target machine.
- `community.docker` Ansible collection.
- `containers.podman collection` Ansible collection if you are using podman

---

## Roles

- **timeseer**: Sets up Timeseer in docker, handling storage preparation,docker volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer)

- **timeseer_reverse_proxy**: Configures Timeseer and its reverse proxy in docker, including storage, docker containers, and proxy setup.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_reverse_proxy-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer_reverse_proxy)

- **timeseer_traefik**: Deploys Traefik as a reverse proxy and load balancer in docker, focusing on SSL/TLS with Let's Encrypt.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_traefik-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/traefik)

- **timeseer_data_service**: Deploys timeseer_data_service ,handling storage preparation,docker volumes, and deployment.  
  [![Ansible Galaxy](https://img.shields.io/badge/docker-timeseer_data_service-yellow.svg)](https://github.com/timeseer-ai/timeseer-ansible-collection/tree/master/timeseer/docker/roles/timeseer_data_service)

---

### Installing timeseer.docker Collection with Ansible

To install the timeseer.docker Ansible collection, use the `ansible-galaxy` tool included with Ansible.

#### Default Installation from Ansible Galaxy

Install the latest version with the following command:

```bash
$ ansible-galaxy collection install timeseer.docker
```

## Installing a Specific Version

To install a specific version, append the version number:

```bash
$ ansible-galaxy collection install timeseer.docker:1.0.7
```

## Overwriting Existing Collections

Use --force to overwrite any existing collections:

```bash
$ ansible-galaxy collection install --force timeseer.docker:1.0.7
```

More information can be found in the [official Ansible documentation](https://docs.ansible.com/).
