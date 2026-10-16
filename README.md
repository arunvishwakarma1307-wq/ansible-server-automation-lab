# Ansible Server Automation Lab

A hands-on Linux System Administration project using Ansible on Ubuntu Linux in WSL 2.

## Project Overview

This project practises Linux configuration management, service management, automation, and idempotency using Ansible playbooks.

**Current scope:** The project currently manages the local Ubuntu environment. Remote Linux servers have not yet been configured.

## Technologies & Tools

- Ubuntu Linux 24.04 LTS (WSL 2)
- Ansible Core 2.21.5
- Python 3.12
- YAML
- Ansible Inventory and Playbooks
- Nginx
- Git and GitHub

## Project Structure

- `inventory.ini` — defines the managed host.
- `nginx_service.yml` — manages Nginx and a test configuration file.
- `README.md` — project documentation.

## Features Implemented

### 1. Ansible Inventory

The `inventory.ini` file defines the local host and Python interpreter:

    [managed]
    localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3.12

### 2. Nginx Service Management

The playbook ensures that:

- Nginx is installed.
- Nginx is running.
- Nginx is enabled to start with the system.

### 3. Configuration Management

Ansible manages the test configuration directory:

`/etc/ansible-lab`

It also creates:

`/etc/ansible-lab/managed.conf`

The file contains:

    project=ansible-server-automation-lab
    managed_by=ansible
    purpose=configuration-management-practice

Verified permissions:

- Directory: `root:root`, mode `0755`
- Configuration file: `root:root`, mode `0644`

### 4. Idempotency

The playbook was run more than once. After the directory and configuration file were created, the second run reported `changed=0` and `failed=0`.

This demonstrates that Ansible does not make unnecessary changes when the system is already in the desired state.

## How to Run

Go to the project directory:

    cd ~/ansible-server-automation-lab

Check inventory connectivity:

    ansible -i inventory.ini managed -m ansible.builtin.ping

Check playbook syntax:

    ansible-playbook -i inventory.ini nginx_service.yml --syntax-check

Perform a dry run:

    ansible-playbook -i inventory.ini nginx_service.yml --check --diff -K

Apply the playbook:

    ansible-playbook -i inventory.ini nginx_service.yml -K

The `-K` option prompts for the privilege-escalation password when needed.

## Verified Test Results

- Ansible installation and version check succeeded.
- Local inventory connectivity returned `SUCCESS` and `ping: pong`.
- Playbook syntax validation passed.
- Dry run completed with `failed=0`.
- Actual playbook execution completed with `failed=0`.
- The configuration file was created and its contents were verified.
- Directory and file permissions were verified.
- A repeat run returned `changed=0` and `failed=0`.

## Current Limitations

- Only the local Ubuntu system is managed.
- Remote Linux hosts and SSH key-based authentication have not yet been configured.
- Multi-server automation has not been tested.
- The project currently covers a small set of Nginx and configuration management tasks.

## Future Improvements

- Add remote Linux managed nodes.
- Configure SSH key-based authentication.
- Manage multiple hosts using an inventory.
- Use variables and templates.
- Automate additional Linux administration tasks.
- Add remote connectivity and failure tests.

## Project Status

**Status: In Progress**

The local Ansible setup, inventory, Nginx management playbook, configuration-file management, and idempotency test have been completed and verified. Remote multi-host automation remains future work.

## Author

Arun Vishwakarma

GitHub: https://github.com/arunvishwakarma1307-wq
