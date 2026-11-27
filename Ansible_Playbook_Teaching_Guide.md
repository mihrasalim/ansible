# Ansible for Beginners: Teaching Your First Playbook

You can explain this playbook to your students as if they are hearing Ansible for the first time.

First, don't start by explaining the YAML syntax. Start with a real-world situation.

## The Playbook

```yaml
---
- name: My first playbook
  hosts: all
  become: true
  tasks:
    - name: Make sure httpd is installed
      dnf:
        name: httpd
        state: present

    - name: Make sure httpd is stopped and disabled
      systemd:
        name: httpd
        state: stopped
        enabled: no
```

---

## 1. Start with a real-world example

Imagine you are a Linux administrator, and your company has 10 Linux servers.

Your manager asks you to do two things on all 10 servers:

1. Install Apache web server.
2. Stop Apache and disable it from starting automatically when the server boots.

You could connect to every server using SSH and execute commands manually.

But doing this manually on 10 servers takes time.

Instead, we can use Ansible to automate these tasks using a playbook.

---

## 2. Explain the playbook line by line

Let's understand what each line means.

### Line 1

```yaml
---
```

This indicates the beginning of a YAML document.

> **Tell students:** "This is how we start our YAML playbook."

### Line 2

```yaml
- name: My first playbook
```

- `name` gives a description to our playbook.
- `My first playbook` is simply the name we chose.

> **Tell students:** "Just like we give names to our files, we give names to our Ansible plays so that we can identify them easily."

### Line 3

```yaml
hosts: all
```

This tells Ansible where to execute the tasks.

- `hosts` specifies the target machines.
- `all` means all the servers listed in the inventory file.

For example, if your inventory contains three worker nodes, Ansible will execute the tasks on all three.

### Line 4

```yaml
become: true
```

This tells Ansible to execute tasks with elevated privileges.

Why?

Because installing or managing system services requires administrator privileges.

It is similar to using:

```bash
sudo
```

> **Tell students:** "We use become because normal users generally don't have permission to install packages or manage system services."

### Line 5

```yaml
tasks:
```

This is where we define the actual work that Ansible should perform.

> **Tell students:** "Until now, we have told Ansible the playbook name, the target servers and the required privileges. Now we are going to tell Ansible what to do."

---

## 3. Explain the first task

```yaml
- name: Make sure httpd is installed
  dnf:
    name: httpd
    state: present
```

Break it into three parts.

### 1. name

```yaml
name: Make sure httpd is installed
```

This is the description of the task.

### 2. dnf

```yaml
dnf:
```

This is an Ansible module.

It manages software packages on systems that use the DNF package manager.

For example, Rocky Linux, AlmaLinux and Fedora.

### 3. name and state

```yaml
name: httpd
state: present
```

- `name: httpd` specifies which package we want to manage.
- `state: present` tells Ansible to make sure the package is installed.

> **Ask students:** "What happens if Apache is already installed?"
>
> **Answer:** Ansible checks the package. If it is already installed, it doesn't reinstall it unnecessarily.

---

## 4. Explain the second task

```yaml
- name: make sure httpd is enabled and running
  systemd:
    name: httpd
    state: stopped
    enabled: no
```

First, explain the module.

`systemd` is an Ansible module used to manage services controlled by systemd.

Now explain each parameter.

| Parameter | Meaning |
|---|---|
| `name: httpd` | Which service should we manage? |
| `state: stopped` | Stop the Apache service now |
| `enabled: no` | Prevent Apache from starting automatically during boot |

**An important distinction for students:**

- `state: stopped` means stop the service immediately.
- `enabled: no` means don't start the service automatically when the server boots.

These are two different operations.

---

## 5. Show students the actual Linux commands

This is one of the best ways to connect Ansible with Linux administration.

| Ansible playbook | Equivalent Linux command |
|---|---|
| `dnf: name=httpd state=present` | `sudo dnf install httpd -y` |
| `systemd: name=httpd state=stopped` | `sudo systemctl stop httpd` |
| `systemd: name=httpd enabled=no` | `sudo systemctl disable httpd` |

> **Tell students:** "Whatever we do manually using Linux commands, we can often automate using Ansible modules."

---

## 6. Demonstrate it practically

First, execute the playbook:

```bash
ansible-playbook apache.yml
```

Then verify the package:

```bash
rpm -q httpd
```

Check the service:

```bash
systemctl status httpd
```

Check whether it is enabled:

```bash
systemctl is-enabled httpd
```

**Expected results:**

- Package: Installed.
- Service: Inactive (stopped).
- Enabled status: Disabled.

### Important teaching point

Your second task's description says:

```yaml
- name: make sure httpd is enabled and running
```

But your actual configuration says:

```yaml
state: stopped
enabled: no
```

The description and configuration contradict each other.

Change the task name to:

```yaml
- name: Make sure httpd is stopped and disabled
```

---

## 7. Final explanation you can speak in class

You can explain the entire playbook in these words:

> "Guys, imagine we have multiple Linux servers, and our requirement is to install Apache on all of them. But after installation, we don't want Apache to run.
>
> Instead of connecting to every server and executing Linux commands manually, we can write an Ansible playbook.
>
> First, we specify the target servers using hosts. Here, we use all, which means all the servers in our inventory.
>
> Next, we use become true because installing packages and managing services require administrator privileges.
>
> Then we define our tasks.
>
> Our first task uses the DNF module to install the Apache package. The state present means we want to make sure Apache is installed.
>
> Our second task uses the systemd module. Here, stopped means we want to stop Apache, and enabled no means we don't want Apache to start automatically when the server reboots.
>
> That's how we automate multiple Linux administration tasks using a single Ansible playbook."

---

## My teaching suggestion

After explaining this playbook, ask students to modify it so that Apache is **installed, started and enabled**. This will help them understand the difference between `present`, `started`, `stopped`, `enabled` and `disabled`.
