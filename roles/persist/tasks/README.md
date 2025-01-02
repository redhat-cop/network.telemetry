# Network Telemetry Persist

## Overview
The `persist` role enables users to fetch facts for telemetry configurations and store them in a YAML-formatted structure. These host variables can be saved either locally or in a remote data store, acting as a single source of truth (SOT) for network telemetry configurations.

## Features
- Fetch structured facts for telemetry resources and transport settings
- Persist gathered telemetry configurations to local directories or remote SCM repositories
- Enable centralized and version-controlled storage for network telemetry policies
- Support for various telemetry components including transport, subscriptions, and destinations

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   |          Example         |
|:---------------------|:-------------:|:--------:|:----:|:------------------------------------------------------------- |:------------------------:|
| `ansible_network_os` | `""`          | yes      | str  | Network OS for which telemetry facts are being gathered.      | `"cisco.nxos.nxos"`      |
| `data_store`         | `""`          | yes      | dict | Specifies the storage configuration (local or SCM).           | See usage example below. |

## Usage
Below are examples demonstrating how to use the persist role:

```yaml
- name: Persist telemetry configurations to local data store
 hosts: all
 gather_facts: true
 tasks:
   - name: Invoke persist role
     ansible.builtin.include_role:
       name: network.telemetry.persist
     vars:
       ansible_network_os: cisco.nxos.nxos
       data_store:
         local: "~/test_backup"
```
## Example Output
When the playbook is executed, the persisted telemetry configurations will be saved in the specified data store in a structured YAML format.

### Example 2: Persist to SCM repository
In this example, gathered facts are stored in a remote Git repository:
```yaml
---
- name: Persist telemetry configurations to SCM repository
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke persist role
      ansible.builtin.include_role:
        name: network.telemetry.persist
      vars:
        ansible_network_os: cisco.nxos.nxos
        data_store:
          scm:
            parent_directory: "/home/rhel"
            origin:
              url: "{{ gh_scm_url }}"
              token: "{{ gh_token }}"
              user:
                name: "{{ gh_username }}"
                email: "{{ gh_email }}"
```

## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team

