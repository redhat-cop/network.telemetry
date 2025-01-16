# Network Telemetry Detect

## Overview
The `network.telemetry.detect` role identifies configuration drift in telemetry settings by comparing current device configurations against a previously persisted state. This role helps maintain configuration consistency by detecting unauthorized or unexpected changes in telemetry configurations across network devices.

## Features
- Detect changes in telemetry configurations from saved states
- Compare current telemetry transport settings with baseline
- Identify drift in telemetry subscription configurations


## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   |          Example         |
|:---------------------|:-------------:|:--------:|:----:|:------------------------------------------------------------- |:------------------------:|
| `ansible_network_os` | `""`          | no       | str  | Network OS to be used during detection.                       | `"cisco.nxos.nxos"`      |
| `data_store`         | `""`          | yes      | dict | Location of persisted configurations for comparison.          | See usage example below. |

## Usage
Below is an example playbook demonstrating how to use the detect role to identify configuration drift:

```yaml
- name: Detect telemetry configuration drift
  hosts: all
  gather_facts: false
  tasks:
    - name: Include detect role
      ansible.builtin.include_role:
        name: network.telemetry.detect
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
When the playbook is executed successfully, the role will output a report highlighting any detected telemetry configuration drifts.

## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team
