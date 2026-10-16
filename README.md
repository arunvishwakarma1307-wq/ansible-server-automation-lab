# Ansible Server Automation Lab

A hands-on Linux System Administration project using Ansible on Ubuntu Linux in WSL 2. This lab demonstrates SSH-based host connectivity, Nginx service management, configuration-file management, and idempotent automation.

## Project Overview

The goal is to practise managing Linux system state through Ansible inventory files and YAML playbooks instead of performing every configuration task manually.

**Current scope:** Ansible connects to the same Ubuntu/WSL environment over SSH through `127.0.0.1`. A separate remote Linux server has not yet been configured.

## Technologies & Tools

- Ubuntu Linux 24.04 LTS (WSL 2)
- Ansible Core 2.21.5
- Python 3.12
- SSH / OpenSSH
- Ed25519 SSH key authentication
- YAML
- Ansible Inventory and Playbooks
- Nginx
- Linux service and file management
- Git and GitHub

## Project Structure

- `inventory.ini` — defines the SSH-managed host.
- `nginx_service.yml` — manages Nginx and a test configuration file.
- `README.md` — project documentation.

## Features Implemented

### 1. SSH-Based Ansible Inventory

The `inventory.ini` file defines the managed host:

    [managed]
    wsl_ssh ansible_host=127.0.0.1 ansible_user=arunvishwakarma ansible_connection=ssh ansible_port=22 ansible_ssh_private_key_file=/home/arunvishwakarma/.ssh/ansible_lab_ed25519 ansible_python_interpreter=/usr/bin/python3.12

A dedicated Ed25519 SSH key was created for the lab. Its public key was authorized for the local Ubuntu user, and SSH key-based login was verified successfully.

The SSH socket was configured to listen on `127.0.0.1:22` for this local learning lab. The private key is stored outside the project repository and must not be committed to GitHub.

### 2. Nginx Package and Service Management

The `nginx_service.yml` playbook uses Ansible's built-in modules to:

- Ensure the Nginx package is installed.
- Ensure the Nginx service is running.
- Ensure the service is enabled.

### 3. Configuration Directory Management

The playbook ensures this directory exists:

`/etc/ansible-lab`

The directory is configured with owner and group `root:root` and permissions `0755`.

### 4. Configuration File Management

The playbook manages:

`/etc/ansible-lab/managed.conf`

The file contains:

    project=ansible-server-automation-lab
    managed_by=ansible
    purpose=configuration-management-practice

The file is configured with owner and group `root:root` and permissions `0644`.

### 5. Idempotent Automation

The playbook was run more than once.

During the initial local execution, Ansible created the configuration directory and file. Subsequent executions reported `changed=0` and `failed=0`.

The playbook was also executed through the SSH inventory. The run completed with `ok=5`, `changed=0`, `unreachable=0`, and `failed=0`.

These tests demonstrate that Ansible can repeatedly verify the desired system state without making unnecessary changes when the configuration is already correct.

## How to Run

Go to the project directory:

    cd ~/ansible-server-automation-lab

Test SSH-based Ansible connectivity:

    ansible -i inventory.ini managed -m ansible.builtin.ping

Check playbook syntax:

    ansible-playbook -i inventory.ini nginx_service.yml --syntax-check

Perform a dry run:

    ansible-playbook -i inventory.ini nginx_service.yml --check --diff -K

Apply the playbook:

    ansible-playbook -i inventory.ini nginx_service.yml -K

The `-K` option prompts for the privilege-escalation password when required.

## Verified Test Results

The following checks were completed in the Ubuntu/WSL environment:

- Ansible installation and version verification succeeded.
- SSH key-based login to `127.0.0.1` succeeded.
- Ansible connectivity returned `SUCCESS` and `ping: pong`.
- Playbook syntax validation passed.
- The dry run completed with `failed=0` and `unreachable=0`.
- The playbook successfully verified Nginx package and service state.
- `/etc/ansible-lab/managed.conf` was created and its content verified.
- Directory permissions were verified as `root:root 755`.
- File permissions were verified as `root:root 644`.
- A subsequent run reported `changed=0` and `failed=0`.

## Current Limitations

- Only the current Ubuntu/WSL system is managed.
- The SSH connection uses loopback address `127.0.0.1`; it is not a separate remote server.
- Multi-server automation has not yet been tested.
- The SSH key and inventory paths are configured for the current Linux username and directory structure.

## Future Improvements

- Configure a separate remote Linux managed node.
- Set up SSH key-based authentication for the remote node.
- Create an inventory for multiple hosts.
- Use variables and templates for reusable configuration.
- Automate additional Linux administration tasks.
- Test connectivity failures and recovery.
- Document and version-control future changes.

## Project Status

**Status: In Progress**

The local Ansible setup, SSH key-based connectivity, Nginx management playbook, configuration-file management, and idempotency tests have been completed and verified. Automation of a separate remote Linux server remains future work.

## Author

Arun Vishwakarma

GitHub: https://github.com/arunvishwakarma1307-wq
