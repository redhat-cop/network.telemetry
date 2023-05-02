# Network Telemetry Validated Content

This repository contains the `network.telemetry` Ansible Collection.

## Description

The `network.telemetry` enables user to manage the Telemetry configuration on networking devices and
also setup a Telegraf - Kafka stack to seemlessly integrate with [Event-Driven Ansible](https://www.ansible.com/use-cases/event-driven-automation).

## Tested with Ansible

Tested with ansible-core >=2.13 releases.

## Installation

```
ansible-galaxy collection install git+https://github.com/redhat-cop/network.base,2.0.0

ansible-galaxy collection install git+https://github.com/redhat-cop/network.telemetry
```

You can also include it in a `requirements.yml` file and install it via `ansible-galaxy collection install -r requirements.yml` using the format:

```yaml
collections:
- name: https://github.com/redhat-cop/network.telemetry.git
  type: git
  version: main
```

## Capabilities
- `Gather Telemetry Facts`: Gather telemetry facts from network devices and store it as host_vars (locally or remote), enabling the creation of a telemetry source-of-truth on a per target host basis.

- `Configure Telemetry`: Manage telemetry as a discrete resource on target hosts. Use locally or remotely stored source-of-truth to push (or update) configuration and also detect drifts.

- `Perform Telemetry Health Checks`: Run health checks to validate the telemetry process running on target hosts. Check for transport connection status, verify data collection statistics for sensor paths, and more.

## Usage

### Gather Telemetry Facts with `action: gather`
- This action "gathers" telemetry configuration from the target host and displays it.

```yaml
- name: Gather telemetry configuration
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    ansible.builtin.include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: gather
```

### Build Brownfield Inventory with `action: persist`

- Gather telemetry configuration (as structured data) from the target host and build a source-of-truth.
- This action "persists" the gathered telemetry configuration into a flat-file (`telemetry.yaml`) in YAML format.
- By default, this file will be created under `host_vars/{{ inventory_hostname }}/` directory. You can override this
  by setting the `inventory_directory` variable.

```yaml
- name: Persist telemetry configuration as host_vars
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    ansible.builtin.include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: persist
          inventory_directory: /path/to/inventory
```

### Deploy Telemetry Configuration with `action: deploy`
- This action "deploys" (pushes) telemetry configuration defined in the host_vars (SoT) to the target hosts.

```yaml
- name: Deploy telemetry configuration from host_vars
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: deploy
```

### Perform Telemetry Health Checks with `action: health_check`

```yaml
health_checks.yml
---
- name: Perform telemetry health checks
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    ansible.builtin.include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: health_check
          vars:
            checks:
              - name: transport_connected
```

**Supported Health Checks**

|     Health Check    |        Supported For       |
|:-------------------:|:--------------------------:|
| transport_connected | cisco.nxos <br/> cisco.ios |
|   collector_stats   |         cisco.nxos         |


### Detect configuration drifts with `action: detect`

- This action "detects" drifts by comparing on-box telemetry configuration with the source-of-truth.

```yaml
- name: Detect drifts in telemetry configuration
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: detect
```

### Remediate configuration drifts with `action: remediate`

- This action "remediates" drifts between on-box telemetry configuration and the source-of-truth.

```yaml
- name: Remediate drifts in telemetry configuration
  hosts: nxos-9k
  gather_facts: false
  tasks:
  - name: Run Telemetry Manager
    include_role:
      name: network.telemetry.run
    vars:
      ansible_network_os: cisco.nxos.nxos
      actions:
        - name: remediate
```

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.


## Release notes

Release notes are available [here](https://github.com/redhat-cop/network.telemetry/blob/main/CHANGELOG.rst).

## Licensing

GNU General Public License v3.0 or later.

See [COPYING](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
