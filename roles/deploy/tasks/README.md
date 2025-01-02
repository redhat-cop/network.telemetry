# Network Telemetry Deploy

## Overview
The `network.telemetry.deploy` role reads telemetry configuration facts from a provided/default or remote inventory and deploys the changes onto network appliances. It enables users to read the config as host_vars from a local or remote data store and deploy telemetry configurations if any changes are detected. This role is designed to ensure that network telemetry configurations remain consistent and efficient across devices.

## Features
- Read telemetry configuration data from local or remote repositories
- Deploy telemetry configurations only when changes are detected
- Support for integration with Git repositories for version control
- Maintain consistent telemetry policies across network infrastructure
- Automated cleanup and persistence of telemetry configurations

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   |          Example         |
|:---------------------|:-------------:|:--------:|:----:|:------------------------------------------------------------- |:------------------------:|
| `ansible_network_os` | `""`          | no       | str  | Network OS to be used during deploy.                          | `"cisco.nxos.nxos"`      |
| `data_store`         | `""`          | yes      | dict | Defines the source of the configurations (local or remote).   | See usage example below. |

## Usage
Below is an example playbook demonstrating how to use the deploy role to retrieve and deploy telemetry configurations:

```yaml
- name: Deploy telemetry configurations
  hosts: all
  gather_facts: true
  tasks:
    - name: Include deploy role
      ansible.builtin.include_role:
        name: network.telemetry.deploy
      vars:
        data_store:
          scm:
            origin:
              url: "{{ gh_scm_path }}"
              token: "{{ gh_token }}"
              user:
                name: "{{ gh_username }}"
                email: "{{ gh_user_email }}"
```
## Example Output
When the playbook is executed successfully, the role will output a report showing the deployment status of telemetry configurations.
## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team
