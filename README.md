# Ansible

A practical learning repository for **Ansible automation**, covering configuration management, server administration, and infrastructure automation.

## What is Ansible?

Ansible is an open-source automation tool used to configure systems, deploy applications, and automate repetitive IT tasks. It uses SSH to connect to managed nodes and usually does not require an agent on those nodes.

## Architecture

- **Control node:** The machine where Ansible is installed and commands or playbooks are executed.
- **Managed nodes:** Remote servers that Ansible manages.
- **Inventory:** A list of managed hosts and host groups.
- **Modules:** Units of work Ansible uses to perform tasks, such as installing packages or managing services.
- **Playbooks:** YAML files that describe automation tasks.

## Topics Covered

- Ansible installation and configuration
- SSH connectivity and key-based authentication
- Inventory files and host/group variables
- Ad-hoc commands and modules
- Playbooks, variables, and facts
- Privilege escalation with `become`
- Handlers, templates, and files
- Roles and reusable task structures
- Ansible Vault for protecting sensitive data

## Basic Commands

Check the installed version:

```bash
ansible --version
```

Check connectivity to all inventory hosts:

```bash
ansible all -m ping
```

List inventory hosts:

```bash
ansible all --list-hosts
```

Run an ad-hoc command on all hosts:

```bash
ansible all -m command -a "uptime"
```

Run a playbook:

```bash
ansible-playbook playbook.yml
```

Run a playbook with privilege escalation and prompt for the become password:

```bash
ansible-playbook playbook.yml --become --ask-become-pass
```

## Example Inventory

Create an `inventory.yml` file:

```yaml
all:
  children:
    webservers:
      hosts:
        web1:
          ansible_host: 192.168.1.10
        web2:
          ansible_host: 192.168.1.11
      vars:
        ansible_user: ubuntu
```

Replace the example IP addresses and SSH user with values from your own lab environment.

## Example Playbook

The following playbook installs and starts Nginx on Debian/Ubuntu managed nodes:

```yaml
---
- name: Install and start Nginx
  hosts: webservers
  become: true

  tasks:
    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Ensure Nginx is running and enabled
      ansible.builtin.systemd:
        name: nginx
        state: started
        enabled: true
```

Run it with:

```bash
ansible-playbook -i inventory.yml nginx.yml
```

## Ansible Vault

Vault encrypts sensitive data such as passwords and secrets.

Create an encrypted variables file:

```bash
ansible-vault create vault.yml
```

Edit it:

```bash
ansible-vault edit vault.yml
```

Run a playbook that uses the encrypted file:

```bash
ansible-playbook -i inventory.yml playbook.yml --ask-vault-pass
```

## Requirements

- A Linux control node (or another supported Ansible control environment)
- Ansible installed on the control node
- SSH connectivity from the control node to managed nodes
- A valid SSH user and authentication method
- Python available on managed nodes for most POSIX modules

## Learning Goal

Use this repository to practise Ansible concepts through commands, YAML files, playbooks, and hands-on lab exercises. Test examples in a lab environment and adapt package managers and service names to the target operating system.

## Useful Documentation

- [Ansible documentation](https://docs.ansible.com/)
- [Ansible playbooks](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html)
- [Ansible modules](https://docs.ansible.com/ansible/latest/collections/index_module.html)
- [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html)
