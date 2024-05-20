# timeseer-ansible-collection

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_ansible_collection-yellow.svg)](https://galaxy.ansible.com/diogolobo122/timeseer)

timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Requirements

- Ansible 2.9 or higher.
- docker installed on the target machine.
- `community.docker` Ansible collection.
- `containers.podman collection` Ansible collection if you are using podman


## Role Implementation Examples

The Timeseer Ansible collection includes four roles.

The `timeseer` role is primarily designed for deploying just the Docker container and is ideal for a limited proof of concept (POC) involving 1-2 users. This setup does not require authentication or a web server and can be run locally. However, for an optimal experience, it is recommended to use a sufficiently powerful machine.

By default, this role creates a directory at `/opt/timeseer`. Within this directory, it also creates `db` and `data` subdirectories for your database and data on your local machine. The default variable definitions can be modified to fit your system requirements. Use the variable `timeseer_dir` to change the default directory.

Timeseer uses TOML files for configuration, and the container includes a default configuration file. However, you will likely need to add your own configuration files. You can use the `timeseer_config_dir` variable to define a directory where you can add TOML files, which will be included in the configuration.

Here is an example source file. You can define a source using TOML. For more information on how to set up sources, please refer to our documentation.



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

To run a simple Docker container Proof of Concept (PoC) using this role, you will need to expose the VM ports. You can use the `timeseer_ports` variable for this purpose. Set the `timeseer_ports` variable to an unused local port, and you will be able to access Timeseer at `localhost:<port>`.

Here is an example of a full YAML configuration with the default directories and exposing port 8080 on the host to 8080 on the container:

```yaml
- name: Setup Timeseer environment
  hosts: test
  become: true
  vars:
    timeseer_config_dir: "/opt/timeseer/config"
    timeseer_ports: 8080:8080

  tasks:
    - name: Import Timeseer Docker role
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"
```




### Timeseer Reverse Proxy role Configuration Guide

When a more robust setup is desired, deploying the `timeseer_reverse_proxy` is recommended. By default, Timeseer includes a reverse proxy facilitated through another Timeseer container.
the  `timeseer_reverse_proxy` role creates a default directory on your system located at `/opt/timeseer/reverse-proxy` to change the directory to one of your preferece use the 
`timeseer_reverse_proxy_dir` variable

he will also create a directory for a secret file that we use to add a secret used for authentication through Azure AD, Google, or a SAML Identity Provider  relative to `timeseer_reverse_proxy_dir/env` is optional to use this file but is recommended as it keeps your secrets from accidently being commited to a repository.


To configure the reverse proxy to work with your environment, you will also need  to configure a TOML file. 

The `timeseer_reverse_proxy_config_dir:` variable can be used as a drop-in directory where you can add TOML files that will be included in the configuration.





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

You can map an external port to this container using the `timeseer_reverse_proxy_ports` variable:

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
  hosts: test
  become: true
  vars:
    timeseer_config_dir: "/opt/timeseer/config"
    timeseer_image: container.timeseer.ai/timeseer
    timeseer_reverse_proxy_image: container.timeseer.ai/timeseer
    timeseer_reverse_proxy_config_dir::  "/opt/timeseer/proxy_config"
    timeseer_reverse_proxy_ports: "8080:8000"
  tasks:
    - name: Import Timeseer docker role for Timeseer
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer"

    - name: Install timeseer reverse proxy
      ansible.builtin.import_role:
        name: "timeseer.docker.timeseer_reverse_proxy"

```
and here a minimal  TOML file to go with this Yaml as an example

```toml
[timeseer]
url = "http://timeseer:8080"
[web]
host = "0.0.0.0"
```




The reverse proxy efficiently handles SSL termination and supports user authentication through Azure AD, Google, or a SAML Identity Provider.

For more information in how to setup SSL and authentication, please refer to our documentation.





### Timeseer traefik role Configuration Guide

We also offer `traefik` as an external reverse proxy; among the  benefits of using Traefik is that he automatically handles SSL termination and supports Let's Encrypt integration for automatic certificate generation and renewal using the ACME protocol, it also supports various authentication methods, including basic auth,  OAuth, and integration with external authentication providers, it also comes with  High Availability: Traefik can be deployed in a highly available configuration with its load balancer , ensuring continuous operation even in the face of failures.


To use Traefik in your deployment, you will need to specify a host via the variable `traefik_host`. This variable expects a host address, which will be used to validate your generated certificate.

Additionally, you also need to specify `traefik_letsencrypt_mail: "`, which is used to validate you as the owner and user of the certificate.

If you prefer to use your own certificate with Traefik, you can set `traefik_tls` to "certs" and provide the names of the certificate and key files via `traefik_cert_file_name` and `traefik_key_file_name`, respectively.

Store these certificates in a directory of your choosing and  use `traefik_certs_config_dir:` to refer to this directory

Import the role using the following YAML:
```yaml
- name: Import Timeseer podman role for Timeseer Traefik
  ansible.builtin.import_role:
    name: "timeseer.podman.traefik"
```



---
The `timeseer data service` is an optional component designed to enhance performance for data services in fleet scenarios. It operates as a distinct service and comes with its own configuration file.
The default configuration file is `Timeseer-data-service.toml`. An alternative config file location can be set using the `timeseer_data_service_toml_dir`


To utilize this service, set the following in your Timeseer configuration TOML file:

```toml
[data-service]
url = "http://timeseer-data-service:3000"
```

By default, it uses port `3000:3000`, but you can change this to modify use docker CLI syntax, such as
     - "3003:3000" (maps host port 3003 to container port 3000)

```yaml
timeseer_data_service_ports:
  - "3003:3000"
```

Additionally, you need to upgrade the data service version to `3` by navigating to `Resources > Manage Resources > Export Resources` on the Timeseer application page. Export the data service you wish to use with the remote data service and update its version to `version: 3`. The default version is `1`.

Import the role using the following YAML:

```yaml
- name: install timeseer data-service
  ansible.builtin.import_role:
    name: "timeseer.docker.timeseer_data_service"
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
