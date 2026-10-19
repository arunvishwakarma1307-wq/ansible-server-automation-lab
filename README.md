# Ansible Server Automation Lab

A hands-on Linux System Administration project using Ansible on Ubuntu Linux in WSL 2. This project demonstrates SSH-based host connectivity, Nginx package management, configuration-file management, Linux user management, SSH key authentication, Docker-based managed-node automation, and idempotent configuration management.

## Project Overview

The goal of this project is to practise managing Linux systems through Ansible inventory files and YAML playbooks rather than performing every configuration task manually.

The lab includes two managed environments:

- **Local Ubuntu/WSL host:** Managed over SSH using a dedicated Ed25519 key.
- **Docker-based Ubuntu managed node:** A separate Linux container accessed over SSH through a published local port, using a dedicated Ed25519 key.

The Docker node provides a separate Linux environment for practising remote Ansible automation without requiring a separate physical server or cloud VM.

## Technologies & Tools

- Ubuntu Linux 24.04 LTS (WSL 2)
- Ansible Core 2.21.5
- Python 3.12
- Docker Desktop with WSL integration
- Ubuntu 24.04 Docker container
- SSH / OpenSSH
- Ed25519 SSH key authentication
- YAML
- Ansible Inventory and Playbooks
- Nginx
- Linux user, service, and file management
- Git and GitHub

## Project Structure

- `inventory.ini` — defines the local Ubuntu/WSL host and Docker managed node.
- `nginx_service.yml` — manages Nginx, its service, and a sample configuration file on the local Ubuntu/WSL host.
- `user_management.yml` — manages a dedicated Linux lab user.
- `docker_node_test.yml` — installs Nginx and manages a sample configuration file on the Docker managed node.
- `README.md` — project documentation.

## Features Implemented

### 1. SSH-Based Ansible Inventory

The `inventory.ini` file defines the managed hosts in separate groups.

The local Ubuntu/WSL host uses the `managed` group. The Docker-based Linux node uses the `docker_nodes` group.

Example inventory structure:

    [managed]
    wsl_ssh ansible_host=127.0.0.1 ansible_user=arunvishwakarma ansible_connection=ssh ansible_port=22 ansible_ssh_private_key_file=/home/arunvishwakarma/.ssh/ansible_lab_ed25519 ansible_python_interpreter=/usr/bin/python3.12

    [docker_nodes]
    docker_managed ansible_host=127.0.0.1 ansible_user=ansibleuser ansible_connection=ssh ansible_port=2222 ansible_ssh_private_key_file=/home/arunvishwakarma/.ssh/ansible_docker_lab_ed25519 ansible_python_interpreter=/usr/bin/python3

The Docker container's SSH port is published on `127.0.0.1:2222`. Although both hosts use the loopback address, the Docker node runs in a separate container with its own Linux filesystem and user account.

Dedicated Ed25519 keys were created for the two environments. Password-based inventory authentication was replaced with SSH key authentication for the Docker node.

**Security note:** Private SSH keys are stored outside the project directory in `~/.ssh/`. Private keys must never be committed to GitHub. The inventory contains environment-specific paths and should be adjusted for another machine or Linux username.

### 2. Local Ubuntu/WSL Nginx Management

The `nginx_service.yml` playbook uses Ansible's built-in modules to:

- Ensure the Nginx package is installed.
- Ensure the Nginx service is running.
- Ensure the service is enabled at startup.
- Create a sample configuration directory.
- Manage a sample configuration file.

The Nginx service management tasks were tested on the local Ubuntu/WSL host.

### 3. Configuration Directory Management

The playbooks ensure that this directory exists:

`/etc/ansible-lab`

The directory is configured with owner and group `root:root` and permissions `0755`.

### 4. Configuration File Management

The playbooks manage:

`/etc/ansible-lab/managed.conf`

The file contains:

    project=ansible-server-automation-lab
    managed_by=ansible
    purpose=configuration-management-practice

The file is configured with owner and group `root:root` and permissions `0644`.

This is a sample configuration file managed by Ansible, not an Nginx website configuration file.

### 5. Linux User Management Automation

The `user_management.yml` playbook uses Ansible's built-in user module to:

- Ensure the `ansible_lab_user` Linux account exists.
- Ensure a home directory is created for the account.
- Use privilege escalation to manage the Linux account.
- Maintain the desired state on repeated runs.

The account was verified using the Linux `id` command.

**Note:** The playbook ensures the account exists but does not configure a password or enable SSH login for the new account.

### 6. Docker-Based Linux Managed Node

A separate Ubuntu 24.04 container named `ansible-managed-node` was configured as a managed Linux node.

The container includes:

- OpenSSH server
- Python 3
- Sudo
- Nginx installation capability

An SSH user named `ansibleuser` was created inside the container. SSH key authentication was configured using a dedicated Ed25519 key stored outside the repository.

The Docker node was verified with Ansible's ping module, which returned `SUCCESS` and `pong`.

### 7. Docker Node Automation Playbook

The `docker_node_test.yml` playbook targets the `docker_nodes` inventory group.

It uses Ansible modules to:

- Install Nginx.
- Create `/etc/ansible-lab`.
- Create `/etc/ansible-lab/managed.conf`.
- Set ownership and permissions.
- Maintain the desired configuration on repeated runs.

The playbook deliberately does not manage the Nginx system service inside the standard Docker container because a normal container does not necessarily run systemd.

### 8. Idempotent Automation

Idempotency means repeatedly applying the same desired configuration should not create unnecessary changes once the system is already in the required state.

The Docker node playbook was executed more than once.

The initial execution reported:

- `changed=3`
- `failed=0`
- `unreachable=0`

The subsequent execution reported:

- `changed=0`
- `failed=0`
- `unreachable=0`

The configuration file content was also checked directly on the Docker node.

The local Nginx and configuration-management playbook and the Linux user-management playbook were tested separately.

### 9. Syntax Validation

Syntax checks were completed successfully for all three playbooks:

- `nginx_service.yml`
- `user_management.yml`
- `docker_node_test.yml`

All three syntax checks passed.

## How to Run

Go to the project directory:

    cd ~/ansible-server-automation-lab

### 1. Test Local Ubuntu/WSL Connectivity

    ansible -i inventory.ini managed -m ansible.builtin.ping

A successful connection returns `SUCCESS` and `ping: pong`.

### 2. Test Docker Node Connectivity

    ansible -i inventory.ini docker_nodes -m ansible.builtin.ping

A successful connection returns `SUCCESS` and `ping: pong`.

### 3. Check Playbook Syntax

    ansible-playbook --syntax-check -i inventory.ini nginx_service.yml
    ansible-playbook --syntax-check -i inventory.ini user_management.yml
    ansible-playbook --syntax-check -i inventory.ini docker_node_test.yml

### 4. Perform a Local Nginx Dry Run

    ansible-playbook -i inventory.ini nginx_service.yml --check --diff -K

Check mode simulates supported changes without normally applying them. Some tasks may behave differently in check mode than during a real run.

### 5. Apply Local Nginx and Configuration Management

    ansible-playbook -i inventory.ini nginx_service.yml -K

### 6. Apply Linux User Management

    ansible-playbook -i inventory.ini user_management.yml -K

The `-K` option prompts for the privilege-escalation password when required.

### 7. Apply Docker Node Automation

    ansible-playbook -i inventory.ini docker_node_test.yml

This playbook installs Nginx and manages the sample configuration directory and file on the Docker managed node.

### 8. Verify the Docker Configuration File

    ansible docker_managed -i inventory.ini -b -m ansible.builtin.command -a "cat /etc/ansible-lab/managed.conf"

The output should contain the three sample configuration lines shown above.

**Prerequisite:** Docker Desktop must be running, Ubuntu WSL integration must be enabled, and the `ansible-managed-node` container must be running with SSH available on port `2222`.

## Verification and Test Results

The following checks were completed during the lab:

- Ansible installation and version verification succeeded.
- SSH key-based login to the local Ubuntu/WSL host succeeded.
- Docker node SSH key authentication was configured.
- Ansible connectivity to the Docker node returned `SUCCESS` and `pong`.
- Syntax validation passed for all three playbooks.
- The local Nginx and configuration-management tasks were tested.
- The `ansible_lab_user` account was created and verified.
- The Docker node Nginx package installation succeeded.
- `/etc/ansible-lab/managed.conf` was created and its content verified on the Docker node.
- The Docker playbook's subsequent run reported `changed=0` and `failed=0`.
- No hosts were unreachable during the successful Docker playbook runs.

## Current Limitations

- The Docker managed node is a local container, not a cloud VM or a separate physical server.
- The Docker node is intended for package and file-management practice; systemd-based service management inside the container has not been configured.
- The Docker container must be running for SSH and Ansible operations to succeed.
- Inventory paths are specific to the current Linux username and environment.
- The user-management playbook does not configure a password or SSH access for the lab account.
- This project focuses on foundational Ansible automation rather than production server hardening.

## Future Improvements

- Configure a cloud-based Linux VM as an additional managed node.
- Expand automation to multiple independent hosts.
- Use Ansible Vault for managing sensitive variables if the project later requires credentials.
- Use templates and variables for reusable configuration.
- Automate additional Linux administration tasks.
- Add controlled failure and recovery tests.

## Project Status

**Status: Core lab implementation and verification completed.**

The local Ubuntu/WSL setup, SSH connectivity, Nginx management, configuration-file management, Linux user management, and Docker-based Linux managed-node automation have been implemented and tested.

The Docker playbook passed syntax validation, successfully applied the desired configuration, and returned `changed=0` on a subsequent run. Future work can expand the lab to cloud-based and multi-server environments.

## Author

Arun Vishwakarma

GitHub: https://github.com/arunvishwakarma1307-wq
