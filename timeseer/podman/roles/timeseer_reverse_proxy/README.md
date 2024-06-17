---

# Ansible Role: timeseer_reverse_proxy

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer_reverse_proxy-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)


This Ansible role facilitates the setup and configuration of Timeseer and its reverse proxy in a podman environment. The role includes tasks for preparing storage directories, configuring podman volumes, managing podman containers for timeseer, and setting up a reverse proxy for Timeseer.

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community.podman` Ansible collection.

## Role Overview

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
  ansible.builtin.command: podman restart timeseer-reverse-proxy
```

### Example TOML Configuration
Below is an example configuration specifying the DNS name of your Timeseer instance within the podman network:

```toml
[timeseer]
url = "http://timeseer:8080"

[web]
port = 8000
```

### Port Configuration
By default, the reverse proxy listens on port 8000  within the podman network. To map an external port to this container, modify the `timeseer_reverse_proxy_ports` variable using podman CLI syntax:

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
    install_dir: "/opt/timeseer"

  tasks:
    - name: Import Timeseer podman role
      ansible.builtin.import_role:
        name: "timeseer.podman.timeseer"
      vars:
        timeseer_config_dir: "{{ install_dir }}/config"

    - name: Configure Timeseer
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ install_dir }}/config/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "config/*.toml"
      notify:
        - Restart Timeseer

    - name: Import Timeseer Reverse Proxy role
      ansible.builtin.import_role:
        name: "timeseer.podman.timeseer_reverse_proxy"
      vars:
        timeseer_reverse_proxy_config_dir: "{{ install_dir }}/proxy-config"
        timeseer_reverse_proxy_enable: true
        timeseer_reverse_proxy_ports: 0.0.0.0:8000

    - name: Configure Timeseer Reverse Proxy
      ansible.builtin.copy:
        src: "{{ item }}"
        dest: "{{ install_dir }}/proxy-config/{{ item | basename }}"
        mode: "0644"
      with_fileglob: "proxy-config/*.toml"
      notify:
        - Restart Timeseer Reverse Proxy

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: podman restart timeseer

    - name: Restart Timeseer Reverse Proxy
      ansible.builtin.command: podman restart timeseer-reverse-proxy

```


## Role Variables

Here are the variables used in this role, complete with their default values found in defaults/main.yml:

- `timeseer_reverse_proxy_dir`: Directory for the reverse proxy configuration. (default: `"/opt/timeseer/reverse_proxy"`)
- `timeseer_reverse_proxy_image`: podman image for the Timeseer reverse proxy. (default: `"podman.timeseer.ai/reverse_proxy"`)
- `timeseer_reverse_proxy_config_dir`: Optional directory for custom reverse proxy configurations. (default: `""`)
- `timeseer_reverse_proxy_journal_tag`: Tag for journal logging. (default: `"TIMESEER_PROXY"`)
- `timeseer_reverse_proxy_ports`: Custom port mapping for the Timeseer reverse proxy container. This should be specified in the podman port mapping format (e.g., `"8080:8080"`). (default: `""`)
---

## Dependencies

No additional roles are required. Ensure podman is properly installed and configured on the target hosts.

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer.

---
