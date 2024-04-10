# timeseer-ansible-collection

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_ansible_collection-yellow.svg)](https://galaxy.ansible.com/diogolobo122/timeseer)

timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Requirements

- Ansible 2.9 or higher.
- docker installed on the target machine.
- `community.docker` Ansible collection.
- `containers.podman collection` Ansible collection if you are using podman

## Example Playbook

The Timeseer Ansible collection includes three roles.

The `timeseer` role is recommended for deploying just the Docker container. It is suitable for a limited proof of concept (POC) with 1-2 users. It does not require authentication or any web server and can be run locally. However, it is recommended to use a sufficiently powerful machine for a good experience. Although it is a  container, it cannot be utilized on Azure Web Apps.


After installing the collection, import the role using the following YAML:

```yaml
- hosts: your_target_hosts
  tasks:
    - name: Run Timeseer container
      ansible.builtin.import_role:
        name: timeseer.docker.timeseer
```

The `timeseer_reverse_proxy` is used when you want a more robust deployment. Timeseer ships with a reverse proxy by default. The reverse proxy is deployed using another Timeseer container, and the configurations are specified in the TOML file.

To redirect connections to the reverse proxy, you will need to modify the TOML file that comes by default with the container. The default name of the config file is `Timeseer-reverse-proxy.toml`.

Example:

```toml
[timeseer]
url = "http://localhost:8080"
```
In the example above, the URL is the host of your Timeseer instance.

You can also use any TOML file if you use our drop-in `proxy-config` variable. Set it, and you can use any TOML file that's inside this directory for the reverse proxy. 

The reverse proxy is capable of terminating SSL and authenticating users using an Azure AD, Google, or SAML Identity Provider. With the reverse proxy, you can enable multi-tenancy, allowing more users to operate different flows in the same environment.

Below is a sample TOML file with authentication and certificates:

```toml
[AzureAD]
application_id = "" # Application (client) ID of the registered application
secret = "" # Client secret
secret_env = "" # Environment variable containing the client secret
tenant = "" # The Tenant ID of the application
key = "" # Path to file containing PEM-encoded key
certificate = "" # Path to file containing PEM-encoded certificate(s)
```

Import the role using the following YAML:

```yaml
- name: Run Timeseer reverse proxy container
  ansible.builtin.import_role:
    name: timeseer.docker.timeseer_reverse_proxy
```

## Installation

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
$ ansible-galaxy collection install timeseer.docker:1.0.2
```

## Overwriting Existing Collections

Use --force to overwrite any existing collections:

```bash
$ ansible-galaxy collection install --force timeseer.docker:1.0.2
```

More information can be found in the [official Ansible documentation](https://docs.ansible.com/).
