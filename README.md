# timeseer-ansible-collection

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_ansible_collection-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Requirements

- Ansible 2.9 or higher.
- docker installed on the target machine.
- `community.docker` Ansible collection.
- `containers.podman collection` Ansible collection if you are using podman

## Role Implementation Examples

## Timeseer Ansible Collection: timeseer Role Overview

The `timeseer` role is primarily intended for deploying the Timeseer Docker container. It is ideally suited for a limited Proof of Concept (POC) involving 1-2 users. This setup does not require authentication or a web server and can be run locally.

### Installation Directory

By default, the `timeseer` role creates the following directories on your local machine:
- **Main directory:** `/opt/timeseer`
- **Database directory:** `/opt/timeseer/db`
- **Data directory:** `/opt/timeseer/data`

To customize the installation directory, modify the `timeseer_dir` variable in your Ansible playbook.

### Configuration

Timeseer uses TOML files for configuration. The Docker container comes with a default configuration file, but additional configuration files are often necessary for adding sources.

- **Configuration directory:** Use the `timeseer_config_dir` variable to specify a directory for your TOML files. 
  
- **Copying Configuration Files:** Utilize the `ansible.builtin.copy` module to copy TOML files from the local machine (where the playbook is run) to the target machine where the Timeseer tasks are being executed.

### Example Configuration

#### Ansible Playbook Example

This YAML snippet demonstrates how to configure and restart Timeseer using `ansible.builtin.copy` module.

```yaml
- name: Configure Timeseer
  ansible.builtin.copy:
    src: "{{ item }}"
    dest: "{{ timeseer_config_dir }}/{{ item | basename }}"
    mode: "0644"
  with_fileglob: "config/*.toml"
  notify:
    - Restart Timeseer
```

#### Handler for Restarting Timeseer

```yaml
- name: Restart Timeseer
  ansible.builtin.command: docker restart timeseer
```

#### TOML Configuration Example

Here's an example of a TOML configuration source file for Timeseer:

```toml
[source.example-row]
type = "csv"
format = "row"
path = "/usr/src/app/data/example-row.csv"
header_row = true
tag_columns = ["location", "plant"]
field_columns = ["product", "value"]

[source.example-row.column_mapping]
"ts" = "ts"
"value" = "value"
```

#### CSV Source Data

Example data source in CSV format:

```csv
location,plant,ts,product,value
Antwerp,P1,2020-01-01T00:00:00Z,A,1
Antwerp,P2,2020-01-01T00:00:00Z,A,1
Antwerp,P1,2020-01-02T00:00:00Z,A,2
Antwerp,P1,2020-01-03T00:00:00Z,B,1
Antwerp,P2,2020-01-03T00:00:00Z,A,2
```

### Port Configuration

To configure port mapping in your Ansible playbook, use the `timeseer_ports` variable. This variable should specify the port mapping in the format `"host_port:container_port"`. Here's what each component means:

- **host_port:** This is the port on your local machine (host) where you will access Timeseer.
- **container_port:** This is the port inside the Docker container where Timeseer is set to receive connections.

#### Example Configuration

For example, if you want to access Timeseer at `localhost:8080`, and Timeseer inside the Docker container is configured to listen on port 8080, you would set the `timeseer_ports` variable as follows:

```yaml
timeseer_ports: "8080:8080"
```

This configuration maps port 8080 of the host to port 8080 of the Docker container, enabling access to the Timeseer interface by navigating to `http://localhost:8080`.



### Full Example of YAML Configuration

This YAML example sets up the Timeseer environment and maps necessary ports:

```yaml
- name: Setup Timeseer environment
  hosts: <your host>
  become: true
  vars:
    timeseer_config_dir: "/opt/config"
    timeseer_ports: 8080:8080

  tasks:
    - name: Import Timeseer Docker role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"

    - name: Configure Timeseer
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ timeseer_config_dir }}/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "config/*.toml"
      notify:
        - Restart Timeseer

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: docker restart timeseer
```

### Additional Tips

- **Documentation:** Ensure you review the official Timeseer and Ansible documentation for detailed guidelines on customization and deployment

---

## Timeseer Reverse Proxy Role Configuration Guide

### Overview
When a more robust setup is desired, deploying the `timeseer_reverse_proxy` role is recommended. By default, Timeseer includes a reverse proxy that is facilitated through another Timeseer container.

### Directory Configuration
- **Default Directory**: The `timeseer_reverse_proxy` role creates a default directory at `/opt/timeseer/reverse-proxy`.
- **Customization**: To change the directory to one of your preference, modify the `timeseer_reverse_proxy_dir` variable in your Ansible playbook.
- **env file**: The role also creates a directory to store a env file at `timeseer_reverse_proxy_dir/env`. Using this file is optional but recommended.
  
This file is utilized for authentication with Azure AD, Google, or a SAML Identity Provider. For detailed setup instructions, please refer to our documentation.

### TOML Configuration
- **Configuration Directory**: Use the `timeseer_reverse_proxy_config_dir` variable to specify a drop-in directory for adding TOML files, which will be included in the configuration.
- **Copying Configuration Files:** Utilize the `ansible.builtin.copy` module to copy TOML files from the local machine (where the playbook is run) to the target machine where the Timeseer tasks are being executed.

### Ansible Playbook Example
Here's how to add configuration files  to Timeseer reverse proxy with  `ansible.builtin.copy`  module :

```yaml
- name: Configure Timeseer Reverse Proxy
  ansible.builtin.copy:
    src: "{{ item }}"
    dest: "{{ timeseer_reverse_proxy_config_dir }}/{{ item | basename }}"
    mode: "0644"
  with_fileglob: "proxy-config/*.toml"
  notify:
    - Restart Timeseer Reverse Proxy
```

### Handler for Restart
Add the following handler to your playbook to manage file changes with a restart:

```yaml
- name: Restart Timeseer Reverse Proxy
  ansible.builtin.command: docker restart timeseer-reverse-proxy
```

### Example TOML Configuration
Below is an example configuration specifying the DNS name of your Timeseer instance within the Docker network:

```toml
[timeseer]
url = "http://timeseer:8080"

[web]
port = 8000
```

### Port Configuration
By default, the reverse proxy listens on port 8000  within the Docker network. To map an external port to this container, modify the `timeseer_reverse_proxy_ports` variable using Docker CLI syntax:

```yaml
timeseer_reverse_proxy_ports: "8080:8000" # Maps host port 8080 to container port 8000
```
### Full YAML Configuration Example

Below is a  example of a YAML configuration setup for Timeseer, including default directories and port forwarding. This configuration maps port 8080 on the host to port 8000 on the reverse proxy container:

**Minimal TOML Configuration**
```toml
[timeseer]
url = "http://timeseer:8080"
[web]
host = "0.0.0.0"
```

**Complete Ansible YAML Configuration:**
```yaml
- name: Setup Timeseer environment
  hosts: <your host>
  become: true
  vars:
    timeseer_config_dir: "/opt/config"
    timeseer_reverse_proxy_config_dir: "/opt/proxy-config"
    timeseer_reverse_proxy_ports: "8080:8000"
    timeseer_reverse_proxy_enable: true

  tasks:
    - name: Import Timeseer Docker role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"

    - name: Configure Timeseer
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ timeseer_config_dir }}/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "config/*.toml"
      notify:
        - Restart Timeseer

    - name: Import Timeseer Reverse Proxy role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer_reverse_proxy"

    - name: Configure Timeseer Reverse Proxy
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ timeseer_reverse_proxy_config_dir }}/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "proxy-config/*.toml"
      notify:
        - Restart Timeseer Reverse Proxy

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: docker restart timeseer

    - name: Restart Timeseer Reverse Proxy
      ansible.builtin.command: docker restart timeseer-reverse-proxy
```

This setup provides a detailed guide on how to deploy Timeseer using Docker and Ansible, ensuring the environment is correctly configured and functional for immediate use.




### Timeseer traefik role Configuration Guide

An optional role configures a [Traefik](https://github.com/traefik/traefik) reverse proxy.

Traefik supports generating SSL certificates using [Let's Encrypt](https://letsencrypt.org/).

The role allows configuring Basic Authentication through Traefik,

providing a convenient way to handle authentication in PoC setups.

Note that Timeseer does not provide support for Traefik.

This role is provided for convenience.

Import the role using the following YAML:

```yaml
- name: Import Timeseer podman role for Timeseer Traefik
  ansible.builtin.import_role:
    name: "timeseer.podman.traefik"
```
### Timeseer data service role Configuration Guide

**Timeseer Data Service Role Configuration Guide**

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

**Full Example YAML with All Timeseer Roles:**
```yaml
- name: Setup Timeseer environment
  hosts: test
  become: true
  vars:
    timeseer_config_dir: "/opt/config"
    timeseer_reverse_proxy_config_dir: "/opt/proxy-config"
    timeseer_data_service_config_dir: "/opt/Timeseer-data-service.toml"
    timeseer_reverse_proxy_ports: "8080:8000"
    timeseer_reverse_proxy_enable: true

  tasks:
    - name: Import Timeseer Docker role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"

    - name: Configure Timeseer
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ timeseer_config_dir }}/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "config/*.toml"
      notify:
        - Restart Timeseer

    - name: Import Timeseer Reverse Proxy role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer_reverse_proxy"

    - name: install timeseer data-service
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer_data_service"

    - name: Configure Timeseer Reverse Proxy
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ timeseer_reverse_proxy_config_dir }}/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "proxy-config/*.toml"
      notify:
        - Restart Timeseer Reverse Proxy

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: docker restart timeseer

    - name: Restart Timeseer Reverse Proxy
      ansible.builtin.command: docker restart timeseer-reverse-proxy
```

---

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
