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

The Timeseer Ansible collection includes four roles.

The `timeseer` role is primarily designed for deploying just the Docker container and is ideal for a limited proof of concept (POC) involving 1-2 users.

This setup does not require authentication or a web server and can be run locally.

However, for an optimal experience, it is recommended to use a sufficiently powerful machine.

By default, this role creates a directory at `/opt/timeseer`.

Within this directory, it also creates `db` and `data` subdirectories for your database and data on your local machine.

The default variable definitions can be modified to fit your system requirements.

Use the variable `timeseer_dir` to change the default directory.

Timeseer uses TOML files for configuration, and the container includes a default configuration file.

However, you will likely need to add your own configuration files.

You can use the `timeseer_config_dir` variable to define a directory where you can add TOML files, which will be included in the configuration.

Create a `config` directory relative to your Ansible playbook directory.

Use the `ansible.builtin.copy` module to copy files from the local machine (where the playbook is run) to the target machine where the Ansible tasks are being executed.

Example:

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

Add the following `handler` to your playbook to handle file changes with a restart:

```yaml
- name: Restart Timeseer
  ansible.builtin.command: docker restart timeseer
```

Here is an example source file.

You can define a source using TOML.

For more information on how to set up sources, please refer to our documentation.

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

And here is the example-row.csv:

```csv
location,plant,ts,product,value
Antwerp,P1,2020-01-01T00:00:00Z,A,1
Antwerp,P2,2020-01-01T00:00:00Z,A,1
Antwerp,P1,2020-01-02T00:00:00Z,A,2
Antwerp,P1,2020-01-03T00:00:00Z,B,1
Antwerp,P2,2020-01-03T00:00:00Z,A,2
```

To run a simple Docker container Proof of Concept (PoC) using this role, you will need to expose the VM ports. 

You can use the `timeseer_ports` variable for this purpose. 

Set the `timeseer_ports` variable to an unused local port, and you will be able to access Timeseer at `localhost:<port>`.

Here is an example of a full YAML configuration with the default directories. 

Modify the ports using Docker CLI syntax. For instance:

"8080:8080" maps host port 8080 to container port 8080:

```yaml
- name: Setup Timeseer environment
  hosts: <your host>
  become: true
  vars:
    timeseer_config_dir: "/opt/timeseer/config"
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

    - name: Import Timeseer Docker role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: docker restart timeseer

```

### Timeseer Reverse Proxy role Configuration Guide

When a more robust setup is desired, deploying the `timeseer_reverse_proxy` is recommended.

By default, Timeseer includes a reverse proxy facilitated through another Timeseer container.

The `timeseer_reverse_proxy` role creates a default directory on your system located at `/opt/timeseer/reverse-proxy`.

To change the directory to one of your preference use the `timeseer_reverse_proxy_dir` variable.

He will also create a directory to store a secret file. 

Relative to `timeseer_reverse_proxy_dir/env`, it is optional to use this file but is recommended.

This file is used for authentication with Azure AD, Google, or a SAML Identity Provider.

For more information on how to set up authentication, please read our documentation.


To configure the reverse proxy to work with your environment, you will also need to configure a TOML file.

The `timeseer_reverse_proxy_config_dir:` variable can be used as a drop-in directory where you can add TOML files that will be included in the configuration.

Create a `proxy-config` directory relative to your Ansible playbook directory.

Similar to `timeseer-role`, you can also create that configuration file with a task using `ansible.builtin.copy`:

```yaml
- name: Configure Timeseer
  ansible.builtin.copy:
    src: "{{ item }}"
    dest: "{{ timeseer_reverse_proxy_config_dir }}/{{ item | basename }}"
    mode: "0644"
  with_fileglob: "proxy-config/*.toml"
  notify:
    - Restart Timeseer Reverse Proxy
```

Add the following `handler` to your playbook to manage file changes with a restart:

```yaml
- name: Restart Timeseer
  ansible.builtin.command: docker restart timeseer-reverse-proxy
```

**Example Configuration:**

In the example bellow, the URL specifies the DNS name of your Timeseer instance within the Docker network.

```toml
[timeseer]
url = "http://timeseer:8080"
```

By default, the reverse proxy listens on port 8000 within the Docker network. To change the port it listens on, use:

```yaml
[web]
port = 8000
```


You can map an external port to this container using the `timeseer_reverse_proxy_ports` variable. Modify it using Docker CLI syntax, such as:
- "8080:8000" (maps host port 8080 to container port 8000)

```yaml
timeseer_reverse_proxy_ports: "8080:8000"
```

If you need the reverse proxy to redirect requests made to your URL to Timeseer, use:

```yaml
[web]
url = "<your URL>"
```

Define the host as `0.0.0.0` if you want the reverse proxy to listen on all interfaces for a minimal configuration, or `127.0.0.1` for local access only:

```yaml
[web]
host = "0.0.0.0"
```

Here is an example of a full YAML configuration with the default directories and exposing port 8080 on the host to 8000 on the reverse proxy container:

```yaml
- name: Setup Timeseer environment
  hosts: <your host>
  become: true
  vars:
    timeseer_config_dir: "/opt/timeseer/config"
    timeseer_reverse_proxy_config_dir: "/opt/timeseer/proxy-config"
    timeseer_reverse_proxy_ports: "8080:8000"
    timeseer_image: container.timeseer.ai/timeseer
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

and here a minimal TOML file to go with this Yaml as an example

```toml
[timeseer]
url = "http://timeseer:8080"
[web]
host = "0.0.0.0"
```



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

The `timeseer data service` is an optional component designed to enhance performance for data services in fleet scenarios.

It operates as a distinct service and comes with its own configuration file.

The default configuration file is `Timeseer-data-service.toml`.

An alternative config file location can be set using the `timeseer_data_service_config_dir`.

To utilize this service, set the following in your Timeseer configuration TOML file:

```toml
[data-service]
url = "http://timeseer-data-service:3000"
```

By default, it uses port `3000:3000`,  to modify use docker CLI syntax, such as - "3003:3000" (maps host port 3003 to container port 3000)

```yaml
timeseer_data_service_ports:
  - "3003:3000"
```

Additionally, you need to upgrade the data service version to `3` by navigating to `Resources > Manage Resources > Export Resources` on the Timeseer application page.

Export the data service you wish to use with the remote data service and update its version to `version: 3`.

The default version is `1`.

Import the role using the following YAML:

```yaml
- name: install timeseer data-service
  ansible.builtin.import_role:
    name: "timeseer.docker.timeseer_data_service"
```
And here is full example yaml with all 3 timeseer roles


```yaml
- name: Setup Timeseer environment
  hosts: test
  become: true
  vars:
    timeseer_config_dir: "/opt/timeseer/config"
    timeseer_reverse_proxy_config_dir: "/opt/timeseer/proxy-config"
    timeseer_data_service_config_dir: "/opt/timeseer/Timeseer-data-service.toml"
    timeseer_reverse_proxy_ports: "8080:8000"
    timeseer_reverse_proxy_enable: true
    timeseer_data_service_image: "container.timeseer.ai/timeseer"

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
