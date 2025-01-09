# Deploy Collector

## Overview
The `deploy_collector` role automates the deployment of a telemetry collection infrastructure using Docker and Telegraf. This role is designed to set up a containerized environment for collecting network telemetry data, particularly from Cisco devices using Model-Driven Telemetry (MDT).

## Features
- Automated Docker and Docker Compose installation
- Telegraf configuration for Cisco MDT (Model-Driven Telemetry)
- Containerized deployment for easy scaling and management
- Support for multiple Linux distributions

## Requirements
- Ansible 2.9 or later
- Target hosts running a supported Linux distribution (RHEL, CentOS, Ubuntu, Debian)
- Internet connectivity for Docker image downloads

## Role Variables

| Variable Name | Default Value | Description |
|:--------------|:-------------:|:------------|
| `distro`      | auto-detected | Target host Linux distribution |
| `ansible_user`| -             | Username for deployment |

## Role Structure
deploy_collector/
├── files/
│   └── telegraf/
│       └── telegraf.conf
├── templates/
│   └── docker-compose.yaml.j2
├── tasks/
│   ├── deploy_collector.yaml
│   ├── docker.yaml
│   └── docker_compose.yaml
├── vars
|   └── main.yaml

## Usage
To use this role in your playbook:

```yaml
- hosts: collectors
  roles:
    - deploy_collector
```

## Task Descriptions

### deploy_collector.yaml

- Detects the target host distribution
- Verifies if the detected distribution is supported
- Includes tasks for Docker installation and container deployment


### docker.yaml

- Adds Docker GPG key and repository
- Installs Docker and related packages
- Starts and enables the Docker service


### docker_compose.yaml

- Copies the Docker Compose configuration file
- Copies the Telegraf configuration file
- Starts the Docker containers


## Telegraf Configuration

The role includes a default Telegraf configuration (`telegraf.conf`) for collecting Cisco MDT data:
```
[[inputs.cisco_telemetry_mdt]]
transport = "grpc"
service_address = ":57000"

[[outputs.kafka]]
brokers = ["broker:29092"]
topic = "eda"
data_format = "json"
```

## Docker Compose Configuration

The role uses a Docker Compose template to define and run the following services:

- Zookeeper
- Kafka broker
- Telegraf


## Customization

To customize the deployment, you can modify the following files:

- `files/telegraf/telegraf.conf`: Adjust Telegraf input and output configurations
- `templates/docker-compose.yaml.j2`: Modify container settings or add additional services

## License
GNU General Public License v3.0 or later.
See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information
- Ansible Network Content Team
