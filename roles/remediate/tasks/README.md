# Network Telemetry Remediate

## Overview

The `network.telemetry.remediate` role automatically corrects configuration drift in telemetry settings by comparing current device configurations against a trusted source of truth and applying necessary changes to restore the intended state.

## Features

- Automatic detection and correction of telemetry configuration drift
- Support for various telemetry components including transport and subscriptions
- Backup of existing configuration before remediation
- Detailed reporting of changes made during remediation
- Integration with network.telemetry.persist for source of truth

## Variables

| Variable Name        | Default Value | Required | Type | Description                                 |         Example          |
| :------------------- | :-----------: | :------: | :--: | :------------------------------------------ | :----------------------: |
| `ansible_network_os` |     `""`      |   yes    | str  | Network OS for telemetry remediation.       |   `"cisco.nxos.nxos"`    |
| `data_store`         |     `""`      |   yes    | dict | Location of source of truth configurations. | See usage example below. |

## Usage

Below is an example playbook demonstrating how to use the remediate role:

```yaml
- name: Remediate telemetry configurations
  hosts: all
  gather_facts: false
  tasks:
    - name: Include remediate role
      ansible.builtin.include_role:
        name: network.telemetry.remediate
      vars:
        data_store:
          local: "~/test_backup"
```

## Example Output

When the playbook is executed successfully, the role will output a report showing what telemetry configurations were remediated, including changes to transport settings, destinations, subscriptions, and feature states that were corrected to match the source of truth.

## Example 2: Remediate Telemetry Drift from SCM Repository

This example reads inventory host variables from a remote SCM repository and applies them to the network devices if telemetry configuration drift exists:

**[CAUTION!]**: This operation will replace the running telemetry configurations on the device. Use with care in production environments.

```yaml
---
- name: Remediate telemetry configuration drift from SCM repository
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke remediate role
      ansible.builtin.include_role:
        name: network.telemetry.remediate
      vars:
        data_store:
          scm:
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