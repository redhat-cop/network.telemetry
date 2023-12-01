# Network Telemetry Validated Content
[![CI](https://github.com/redhat-cop/network.telemetry/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/redhat-cop/network.telemetry/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7404/badge)](https://bestpractices.coreinfrastructure.org/projects/7404)

This repository contains the `network.telemetry` Ansible Collection.

## Description

The `network.telemetry` enables user to manage the Telemetry configuration on networking devices and
also setup a Telegraf - Kafka stack to seemlessly integrate with [Event-Driven Ansible](https://www.ansible.com/use-cases/event-driven-automation).

## Tested with Ansible

Tested with ansible-core >=2.14 releases.

## Installation

To consume this Validated Content from Automation Hub, the following needs to be added to `ansible.cfg`:

```
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://cloud.redhat.com/api/automation-hub/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<SuperSecretToken>
```
Get the required token from the [Automation Hub Web UI](https://console.redhat.com/ansible/automation-hub/token).

With this configured, simply run the following commands:

```
ansible-galaxy collection install network.base
ansible-galaxy collection install network.telemetry
```

## Capabilities
- `Gather Telemetry Facts`: Gather telemetry facts from network devices and store it as host_vars (locally or remote), enabling the creation of a telemetry source-of-truth on a per target host basis.

- `Manage Telemetry Configuration`: Configure telemetry as a discrete resource on target hosts. Use locally or remotely stored source-of-truth to push (or update) configuration and also detect/remediate drifts.

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
      operations:
        - name: gather
```

### Build Brownfield Inventory with `action: persist`

- Gather telemetry configuration (as structured data) from the target host and build a source-of-truth.
- This action "persists" the gathered telemetry configuration into a flat-file (`telemetry.yaml`) in YAML format.
- By default, this file will be created under `host_vars/{{ inventory_hostname }}/` directory.
- To persist this data in a remote scm, specify details in the `scm` option under `data_store` key.
- Or, to persist this data locally, please specify the path in the `local` option under the `data_store` key.

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
      operations:
        - name: persist
      data_store:
        scm:
          origin:
            url: "/path/to/remote/scm"
            token: "superSecretToken"
            user:
              name: scm_user_name
        local: ./telemetry_backup # optional

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
      operations:
        - name: deploy
      data_store:
        scm:
          origin:
            url: "/path/to/remote/scm"
            token: "superSecretToken"
            user:
              name: scm_user_name
```

### Perform Telemetry Health Checks with `action: health_check`

```yaml
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
      operations:
        - name: health_check
          vars:
            checks:
              - name: transport_connected
```

**Supported Health Checks**

|     Health Check    |        Supported For       |
|:-------------------:|:--------------------------:|
| transport_connected |         cisco.nxos         |


### Deploy a telemetry collector for use with Event-Driven Ansible

- This action builds a containerized Telegraf - Kafka that collects telemetry from network devices.
- The Telegraf container is configured to receive the data on port 57000 and write it to the Kafka topic `eda`.
- The Kafka container is configured to listen for reads/writes on port `9092`.
- An EDA rulebook can then read the telemetry data from this Kafka topic by leveraging the Kafka source plugin
  and take operations.
- Optionally, you can set the `kafka_external_listener` variable to the IP address of the host where these containers
  are deployed. This is required when EDA is running on a different host.

```yaml
- name: Deploy telemetry collector
  hosts: collector01
  gather_facts: true
  tasks:
  - name: Run Telemetry Manager
    include_role:
      name: network.telemetry.run
    vars:
      operations:
        - name: deploy_collector
          kafka_external_listener: 203.0.113.100 # optional
```
**Example Rulebook**

```yaml
- name: Report incident to ServiceNow
  hosts: nxos
  sources:
    - ansible.eda.kafka:
        host: 203.0.113.100
        topic: eda
        port: 9092
  rules:
    - name: Check route count drops
      condition:
        all:
          - event.fields.routeCount < 100
          - event.tags.vrfName == "prod"
      actions:
        - run_playbook:
            name: report_event.yaml
```

### Support Matrix

|      Action      |               Platform              |
|:----------------:|:-----------------------------------:|
|      deploy      |             Cisco NX-OS             |
|      gather      |             Cisco NX-OS             |
|      persist     |             Cisco NX-OS             |
| deploy_collector | RHEL <br/> CentOS <br/> Fedora<br/> |

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.

## Release notes

Release notes are available [here](https://github.com/redhat-cop/network.telemetry/blob/main/CHANGELOG.rst).

## Contributing to this collection

We welcome community contributions to this collection. If you find problems, please open an issue or create a PR against this repository.

### Testing and Development

The project uses tox to run `ansible-lint` and `ansible-test sanity`.
Assuming this repository is checked out in the proper structure,
e.g. `collections_root/ansible_collections/network/telemetry`, run:

```shell
  tox -e ansible-lint
  tox -e py39-sanity
```

To run integration tests, ensure that your inventory has a `network_telemetry` group.
Depending on what test target you are running, comment out the host(s).

```shell
[network_telemetry]
nxos
collector01

[nxos_9k]
nxos

[collectors]
collector01

[nxos_9k:vars]
< enter inventory details for this group >

[collectors:vars]
< enter inventory details for this group >
```

```shell
  ansible-test network-integration -i /path/to/inventory --python 3.9 [target]
```

## Licensing

GNU General Public License v3.0 or later.

See [COPYING](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
