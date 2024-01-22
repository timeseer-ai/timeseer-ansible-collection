# timeseer-ansible-collection
Timeseer offers a platform for enhancing the reliability and observability of time-series data. It addresses challenges like unreliable data, reducing data downtime, and ensuring trustworthiness in time-series analytics. The platform is designed to assist data teams in detecting, prioritizing, and investigating data quality issues proactively. It integrates seamlessly into modern data stacks, offering features like data reliability scoring, monitoring, optimization, and connectivity. Timeseer.AI is an essential tool for roles such as Data Officers, Engineers, Automation Engineers, and Data Scientists, providing a comprehensive solution for managing time-series data quality effectively.

For more detailed information, visit the [Timeseer.AI website](https://www.timeseer.ai/).

## Installation

### Installing Timeseer.Timeseer Collection with Ansible

To install the Timeseer.Timeseer Ansible collection, use the `ansible-galaxy` tool included with Ansible.

#### Default Installation from Ansible Galaxy
Install the latest version with the following command:
```bash
$ ansible-galaxy collection install timeseer.timeseer
```

## Installing a Specific Version
To install a specific version, append the version number:
```bash
$ ansible-galaxy collection install timeseer.timeseer:1.0.0
```

## Overwriting Existing Collections
Use --force to overwrite any existing collections:
```bash
$ ansible-galaxy collection install --force timeseer.timeseer:1.0.0
```

More information can be found in the [official Ansible documentation](https://docs.ansible.com/).
