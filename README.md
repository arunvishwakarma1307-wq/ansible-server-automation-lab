# Ansible Server Automation Lab

A hands-on Linux System Administration project using Ansible on Ubuntu Linux in WSL 2. This project demonstrates SSH-based host connectivity, Nginx service management, configuration-file management, Linux user management, and idempotent automation.

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
- Linux user, service, and file management
- Git and GitHub

## Project Structure

- `inventory.ini` — defines the SSH-managed host.
- `nginx_service.yml` — manages Nginx and a test configuration file.
- `user_management.yml` — manages a dedicated Linux lab user.
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
- Ensure the service is enabled at startup.

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

This is a sample configuration file managed by Ansible, not an Nginx website configuration file.

### 5. Linux User Management Automation

The `user_management.yml` playbook uses Ansible's built-in user module to:

- Ensure the `ansible_lab_user` Linux account exists.
- Ensure a home directory is created for the account.
- Use privilege escalation to manage the Linux account.
- Maintain the desired state without creating duplicate accounts on repeated runs.

The playbook was executed successfully through the SSH inventory. The account was verified using the Linux `id` command, which returned the user's UID, GID, and group information.

**Note:** The playbook ensures the account exists but does not configure a password or enable SSH login for the new account.

### 6. Idempotent Automation

The Nginx and configuration-management playbook was run more than once.

During the initial execution, Ansible created the configuration directory and file. Subsequent executions reported `changed=0` and `failed=0`.

The playbook was also executed through the SSH inventory. A subsequent run completed with:

- `ok=5`
- `changed=0`
- `unreachable=0`
- `failed=0`

The user-management playbook was also executed successfully, and the created Linux account was verified.

These tests demonstrate how Ansible can repeatedly verify the desired system state without making unnecessary changes when the configuration is already correct.

## How to Run

Go to the project directory:

    cd ~/ansible-server-automation-lab

### 1. Test SSH-Based Ansible Connectivity

    ansible -i inventory.ini managed -m ansible.builtin.ping

A successful connection returns `SUCCESS` and `ping: pong`.

### 2. Check Nginx Playbook Syntax

    ansible-playbook -i inventory.ini nginx_service.yml --syntax-check

### 3. Perform an Nginx Playbook Dry Run

    ansible-playbook -i inventory.ini nginx_service.yml --check --diff -K

Check mode simulates supported changes without normally applying them. Some tasks may behave differently in check mode than during a real run.

### 4. Apply Nginx and Configuration Management

    ansible-playbook -i inventory.ini nginx_service.yml -K

### 5. Check User Management Playbook Syntax

    ansible-playbook -i inventory.ini user_management.yml --syntax-check

### 6. Apply Linux User Management

    ansible-playbook -i inventory.ini user_management.yml -K

The `-K` option prompts for the privilege-escalation password when required.

## Verification and Test Results

The following checks were completed in the Ubuntu/WSL environment:

- Ansible installation and version verification succeeded.
- SSH key-based login to `127.0.0.1` succeeded.
- Ansible connectivity returned `SUCCESS` and `ping: pong`.
- Nginx playbook syntax validation passed.
- The Nginx dry run completed without unreachable hosts or failed tasks.
- The Nginx service and package state were successfully managed.
- `/etc/ansible-lab/managed.conf` was created and its content verified.
- Directory permissions were verified as `root:root 755`.
- File permissions were verified as `root:root 644`.
- The Nginx playbook subsequently reported `changed=0` and `failed=0`.
- User-management playbook syntax validation passed.
- The `ansible_lab_user` account was created successfully.
- The account was verified using the Linux `id` command.

## Current Limitations

- Only the current Ubuntu/WSL system is managed.
- The SSH connection uses loopback address `127.0.0.1`; it is not a separate remote server.
- Multi-server automation has not yet been tested.
- The SSH key and inventory paths are configured for the current Linux username and directory structure.
- The user-management playbook does not configure a password or SSH access for the lab account.

## Future Improvements

- Configure a separate remote Linux managed node.
- Set up SSH key-based authentication for the remote node.
- Create an inventory for multiple hosts.
- Use variables and templates for reusable configuration.
- Automate additional Linux administration tasks.
- Test connectivity failures and recovery.
- Expand user and group management automation.
- Document and version-control future changes.

## Project Status

**Status: In Progress**

The local Ansible setup, SSH key-based connectivity, Nginx management, configuration-file management, Linux user management, and initial idempotency testing have been implemented and verified in the local Ubuntu/WSL environment.

Automation of a separate remote Linux server and multi-server management remain future work.

## Author

Arun Vishwakarma

GitHub: https://github.com/arunvishwakarma1307-wq
