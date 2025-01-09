# Deploy Health Check

## Overview
The `deploy_health_check` role automates monitoring of telemetry configuration health on Cisco NXOS devices. This role verifies telemetry configuration state and transport connectivity.

## Features
- Transport connectivity verification
- Telemetry configuration validation
- Support for custom health checks
- Automated remediation on failures

## Requirements
- Ansible 2.9 or later
- Cisco NXOS devices with telemetry enabled
- Network connectivity to devices

## Role Variables

| Variable Name | Default Value | Required | Description |
|:-------------|:-------------:|:--------:|:------------|
| `telemetry_health_check` | - | yes | Health check configuration |
| `ansible_network_os` | - | yes | Must be cisco.nxos.nxos |

## Role Structure
```yaml
deploy_health_check/
├── tasks/
│   ├── main.yaml           
│   ├── validation.yaml     
│   ├── nxos_checks.yaml    
│   ├── unsupported_platform.yaml
│   └── nxos/
│       └── transport_connected.yaml
```
## Usage
Below are examples demonstrating how to use the persist role:
``` yaml
- hosts: nxos
  tasks:
    - name: Include health check role
      ansible.builtin.include_role:
        name: network.telemetry.health_checks
      vars:
        telemetry_health_check:
          name: health_check
          vars:
            details: true
            checks:
              - name: transport_connected
```
## Example Output
The role performs health checks including Transport Connected Check, which verifies telemetry transport status, collector connectivity, and transmission errors.

## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team
