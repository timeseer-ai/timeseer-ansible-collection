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

Timeseer uses TOML files for its configuration. Below is an example

```toml
data_dir = "db"

[analysis]
workers = 4

[[include]]
glob = "data/*.toml"

[[include]]
glob = "config/*.toml"
```

The container comes with a default configuration file, but if you wish to modify it, you can use the `timeseer_config_dir` variable. Any TOML files placed in this directory will be automatically included in the configuration.


For a quick test, set `timeseer_ports` to a local unused port, and you can access Timeseer at `localhost:<port>`.


After installing the collection, import the role using the following YAML:

```yaml
- hosts: your_target_hosts
  tasks:
    - name: Run Timeseer container
      ansible.builtin.import_role:
        name: timeseer.docker.timeseer
```

---

The `timeseer_reverse_proxy` is deployed when a more robust setup is desired. By default, Timeseer includes a reverse proxy, which is facilitated through another Timeseer container. The configurations for this are specified in a TOML file.

To redirect connections through the reverse proxy, you need to modify the default TOML file provided with the container. The configuration file is  named `Timeseer-reverse-proxy.toml`.

**Example Configuration:**

```toml
[timeseer]
url = "http://timeseer:8080"
```

In the example above, the URL specifies the DNS name of your Timeseer instance within the Docker network. By default, the reverse proxy listens on port 8000. You can map an external port to this service using the variable `timeseer_reverse_proxy_ports`:

```yaml
timeseer_reverse_proxy_ports: "8080:8000"
```

This configuration will redirect all requests made to your specified URL, as demonstrated in the following snippet:

```yaml
[web]
url = "<my address>"
```

These settings ensure that all inbound traffic directed to the specified address is rerouted to the Timeseer container's reverse proxy.

You can utilize any TOML file for the reverse proxy configuration by setting the `proxy-config` variable. Once set, the reverse proxy can reference any TOML file located within the specified directory.

The reverse proxy efficiently handles SSL termination and supports user authentication through Azure AD, Google, or a SAML Identity Provider.

Below is an example of a TOML file configured for authentication and certificates:

```toml
[AzureAD]
application_id = "" # Application (client) ID of the registered application
secret = "" # Client secret
secret_env = "" # Environment variable containing the client secret
tenant = "" # The Tenant ID of the application
key = "" # Path to file containing PEM-encoded key
certificate = "" # Path to file containing PEM-encoded certificate(s)
```



---

We also offer `traefik` as an external reverse proxy; one of the biggest benefits of using Traefik is that it utilizes the ACME protocol, which allows us to generate HTTPS certificates.
To use Traefik in your deployment, you will need to specify a host via the variable `traefik_host`. This variable expects a host address, which will be used to validate your generated certificate.

Additionally, you also need to specify `traefik_letsencrypt_mail: ""`, which is used to validate you as the owner and user of the certificate.

If you prefer to use your own certificate with Traefik, you can set `traefik_tls` to "certs" and provide the names of the certificate and key files via `traefik_cert_file_name` and `traefik_key_file_name`, respectively.

Store these certificates in a directory of your choosing or use `traefik_certs_config_dir: "/opt/timeseer/traefik/certs"`.

Import the role using the following YAML:
```yaml
- name: Import Timeseer podman role for Timeseer Traefik
  ansible.builtin.import_role:
    name: "timeseer.podman.traefik"
```



---
The `timeseer data service` is an optional component designed to enhance performance for data services in fleet scenarios. It operates as a distinct service and comes with its own configuration file.
The default configuration file is `Timeseer-data-service.toml`. An alternative config file location can be set using the `timeseer_data_service_toml_dir`

**Cache**  
By default, 2 GB of memory are allocated for caching data service contents.

```toml
cache_bytes = 2147483648 # 2 GB
```

Set `cache_bytes = 0` to disable caching.



To utilize this service, set the following in your Timeseer configuration TOML file:

```toml
[remote]
url = "http://timeseer-data-service:3000"
```

By default, it uses port `3000:3000`, but you can change this to, for example, `3003:3000` using the variable:

```yaml
timeseer_data_service_ports: "3003:3000"
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
