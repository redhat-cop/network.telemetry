# Network Telemetry Gather

## Overview
The `gather` role enables users to collect and display structured facts for telemetry configurations across network devices. This role helps administrators gain insights into the current state of telemetry settings, subscriptions, and transport configurations in a standardized format.

## Features
- Retrieve structured facts for specified network telemetry resources
- Provides data in YAML format for easy consumption and analysis

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                  |      Example        |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------------------:|
| `ansible_network_os` | `""`          | no       | str  | Network OS to be used during gather.                         | `"cisco.nxos.nxos"` |

## Usage
Below is an example playbook demonstrating how to use the `gather` role to collect telemetry configurations:

```yaml
---
- name: Gather structured facts for telemetry resources
 hosts: all
 gather_facts: true
 tasks:
   - name: Invoke gather role
     ansible.builtin.include_role:
       name: network.telemetry.gather
     vars:
       ansible_network_os: cisco.nxos.nxos
```
## Example Output
When the playbook is executed successfully, the output will display the structured facts for the specified telemetry resources.

## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team
