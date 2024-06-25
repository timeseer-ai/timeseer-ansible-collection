---

# Ansible Role: timeseer

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-timeseer-yellow.svg)](https://galaxy.ansible.com/ui/namespaces/timeseer/)

This Ansible role is designed for setting up timeseer, a time-series data analysis platform, particularly in a podman environment. It handles storage directory preparations, podman volume configurations, and manages the deployment of timeseer in a podman container.

## Requirements

- Ansible 2.9 or higher.
- podman installed on the target machine.
- `community podman` Ansible collection.


## Role Overview

The `timeseer` role is primarily intended for deploying the Timeseer podman container. It is ideally suited for a limited Proof of Concept (POC) involving 1-2 users. This setup does not require authentication or a web server and can be run locally.

### Installation Directory

By default, the `timeseer` role creates the following directories on your local machine:
- **Main directory:** `/opt/timeseer`
- **Database directory:** `/opt/timeseer/db`
- **Data directory:** `/opt/timeseer/data`

To customize the installation directory, modify the `timeseer_dir` variable in your Ansible playbook.

### Configuration

Timeseer uses TOML files for configuration. The podman container comes with a default configuration file, but additional configuration files are often necessary for adding sources.

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
  ansible.builtin.command: podman restart timeseer
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
Data is stored in the Data directory at  `/opt/timeseer/data`. 

Below is example data source in CSV format:


```cs
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
- **container_port:** This is the port inside the podman container where Timeseer is set to receive connections.

#### Example Configuration

For example, if you want to access Timeseer at `localhost:8080`, and Timeseer inside the podman container is configured to listen on port 8080, you would set the `timeseer_ports` variable as follows:

```yaml
timeseer_ports: "8080:8080"
```

This configuration maps port 8080 of the host to port 8080 of the podman container, enabling access to the Timeseer interface by navigating to `http://localhost:8080`.



### Full Example of YAML Configuration

This YAML example sets up the Timeseer environment and maps necessary ports:

```yaml
- name: Setup Timeseer environment
  hosts: <your host>
  become: true
  vars:
    install_dir: "/opt/timeseer"

  tasks:
    - name: Import Timeseer podman role
      ansible.builtin.import_role:
        name: "timeseer podman.timeseer"
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

  handlers:
    - name: Restart Timeseer
      ansible.builtin.command: podman restart timeseer

```

### Additional Tips

- **Documentation:** Ensure you review the official Timeseer and Ansible documentation for detailed guidelines on customization and deployment

## Role Variables

Here are the variables used in this role, complete with their default values found in `defaults/main.yml`:

- `timeseer_dir`: Base directory for Timeseer data and configurations. (default: `"/opt/timeseer"`)
- `timeseer_db_dir`: Directory for Timeseer database files. (default: `"/opt/timeseer"`)
- `timeseer podman_volume`: Optional podman volume for Timeseer storage. (default: `""`)
- `timeseer_network`: Name of the podman network for Timeseer. (default: `"timeseer"`)
- `timeseer_image`: podman image for Timeseer. (default: `"container.timeseer.ai/timeseer"`)
- `timeseer_download_demo_data`: Whether to download demo data for Timeseer. (default: `false`)
- `timeseer_config_dir`: Optional directory for custom Timeseer configurations. (default: `""`)
- `timeseer_ports`: Custom port mapping for the Timeseer container. This should be specified in the podman port mapping format (e.g., `"8080:8080"`). (default: `""`)
- `timeseer_journal_tag`: Tag for journal logging. (default: `"TIMESEER"`)
- `timeseer_image_pull`: (default: `newer`): Controls when to pull the container image, with options "missing", "always", "never", or "newer". 
  - "missing": Pulls image if not present locally.
  - "always": Always pulls image, ensuring the latest version.
  - "never": Never pulls image, uses local version.
  - "newer": Pulls image only if the registry version is newer than the local one.
---

## Dependencies

No other Galaxy roles are dependencies for this role. However, it's important to ensure that podman is properly installed and configured on your hosts.

## License

See the `LICENSE.md` file in this repository.

## Author Information

This role was created in 2024 by timeseer.

---
