# Ansible Playbook 

---

## 1. Real-World Problem Scenario

You are a DevOps engineer managing **many servers**.

You need to:

- Install **Nginx**, **Docker**, **Java** on all app servers
- Create **users**, set **permissions**, and copy **config files**
- Deploy **application code** from Git
- Restart services when config changes

You **can** do this with Ansible ad-hoc commands:

```bash
ansible web -i inventory -m apt -a "name=nginx state=present"
ansible web -i inventory -m copy -a "src=app.conf dest=/etc/app.conf"
ansible web -i inventory -m service -a "name=nginx state=restarted"
```

But problems start:

- You forget which commands you ran
- You cannot easily **repeat** the same steps later
- Your teammate does not know your steps
- Very hard to maintain for **bigger setups**

You need something that:

- Stores **all steps in one file**
- Runs steps in **order**
- Is **repeatable**, **shareable**, and **version-controlled**

That "something" is an **Ansible Playbook**.

---

## 2. What Is an Ansible Playbook?

In simple words:

> An **Ansible Playbook** is a **YAML file** where you write **what you want to do on which servers**, step by step.

Key points:

- It is a **text file** written in **YAML**.
- It contains **plays**.
- Each play has **tasks**.
- Each task uses an Ansible **module** (like `apt`, `copy`, `service`).
- It tells Ansible:
  - **Which hosts** to target (from inventory)
  - **What tasks** to do
  - **In what order**
  - With which **variables**, **conditions**, **handlers**, etc.

Simple example:

```yaml
---
- name: Install and start Nginx on web servers
  hosts: web
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
        enabled: yes
```

---

## 3. Why Do We Use Playbooks?

Reasons to use playbooks instead of only ad-hoc commands:

- **Automation as code**
  - Playbooks **document** your steps.
  - Easy to store in **Git**.

- **Repeatable**
  - Run the same playbook today, tomorrow, next month.
  - Same result every time (idempotent).

- **Shareable**
  - Team members can read and run the same file.
  - Onboarding new people becomes easy.

- **Ordered execution**
  - Tasks run in a **defined order**.
  - Example: install package → copy config → restart service.

- **Maintainable**
  - Easier to maintain big infrastructure with clean YAML files.
  - Easier to refactor into **roles**.

- **Powerful features**
  - Variables, loops, conditions, handlers, templates, roles, tags.

---

## 4. Basic Concepts in a Playbook

Before writing playbooks, understand these simple words:

- **Playbook**
  A YAML file containing one or more **plays**.

- **Play**
  A mapping between:
  - **Hosts** (where to run)
  - **Tasks** (what to do)

- **Task**
  A single action.
  Example: install a package, copy a file, start a service.

- **Module**
  A small program used inside a task.
  Examples: `apt`, `yum`, `copy`, `service`, `user`.

- **Handler**
  Special task that only runs when **notified** (usually for restarting services).

- **Variables**
  Values that can change (like `app_version`, `http_port`) used inside tasks.

- **Templates**
  Files with variables (Jinja2 syntax) that Ansible fills and copies.

- **Roles**
  A way to organize playbooks into reusable components (web, db, monitoring, etc.).

---

## 5. Playbook File Structure (YAML Basics)

YAML rules (very important):

- Use **spaces**, not tabs.
- Indentation is critical (usually **2 spaces**).
- Lists start with `-`.
- Key-value pairs use `key: value`.

Basic structure:

```yaml
---
- name: <Play 1 name>
  hosts: <group or host>
  become: yes/no
  vars:
    key: value

  tasks:
    - name: <Task 1 name>
      module_name:
        option1: value1
        option2: value2

    - name: <Task 2 name>
      module_name:
        option: value

- name: <Play 2 name>
  hosts: <group or host>
  tasks:
    ...
```

---

## 6. How Ansible Executes a Playbook (Flow)

1. You run:

   ```bash
   ansible-playbook playbook.yml -i inventory
   ```

2. Ansible reads:
   - The **inventory** (hosts file)
   - The **playbook** (YAML file)
   - Any **vars files**, **roles**, etc.

3. For each **play**:
   - Selects target **hosts** (e.g., `web` group).
   - Runs each **task** in order **on each host**.
   - If a task **changes something**, it marks the host as "changed".
   - If a task has `notify`, it will call **handlers** at the end.

4. At the end:
   - Shows a summary: ok, changed, failed, skipped.

---

## 7. Simple Playbook Examples

### 7.1 Example 1 – Ping All Hosts

```yaml
---
- name: Ping all hosts
  hosts: all

  tasks:
    - name: Ping using Ansible ping module
      ping:
```

Run:

```bash
ansible-playbook ping.yml -i inventory
```

---

### 7.2 Example 2 – Install Nginx on Web Servers

```yaml
---
- name: Install Nginx on web servers
  hosts: web
  become: yes

  tasks:
    - name: Install Nginx (Debian/Ubuntu)
      apt:
        name: nginx
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Install Nginx (RedHat/CentOS)
      yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat"

    - name: Ensure Nginx is running and enabled
      service:
        name: nginx
        state: started
        enabled: yes
```

---

### 7.3 Example 3 – Create User and Add SSH Key

```yaml
---
- name: Create deploy user and add SSH key
  hosts: all
  become: yes

  vars:
    deploy_user: deploy
    deploy_ssh_key: "ssh-rsa AAAA....your-public-key...."

  tasks:
    - name: Create deploy user
      user:
        name: "{{ deploy_user }}"
        state: present
        create_home: yes

    - name: Add SSH key for deploy user
      authorized_key:
        user: "{{ deploy_user }}"
        key: "{{ deploy_ssh_key }}"
```

---

### 7.4 Example 4 – Deploy Application from Git

```yaml
---
- name: Deploy application
  hosts: web
  become: yes

  vars:
    app_repo: https://github.com/user/myapp.git
    app_dir: /var/www/myapp
    app_version: v1.2.0

  tasks:
    - name: Clone or update git repository
      git:
        repo: "{{ app_repo }}"
        dest: "{{ app_dir }}"
        version: "{{ app_version }}"

    - name: Install dependencies
      command: "npm install"
      args:
        chdir: "{{ app_dir }}"

    - name: Copy systemd service file
      template:
        src: templates/myapp.service.j2
        dest: /etc/systemd/system/myapp.service
      notify: Restart myapp

    - name: Start application
      systemd:
        name: myapp
        state: started
        enabled: yes

  handlers:
    - name: Restart myapp
      systemd:
        name: myapp
        state: restarted
```

---

### 7.5 Example 5 – Multiple Plays in One Playbook

```yaml
---
- name: Setup web servers
  hosts: web
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx
      service:
        name: nginx
        state: started

- name: Setup database servers
  hosts: db
  become: yes

  tasks:
    - name: Install MySQL
      apt:
        name: mysql-server
        state: present

    - name: Start MySQL
      service:
        name: mysql
        state: started

- name: Configure monitoring on all servers
  hosts: all
  become: yes

  tasks:
    - name: Install monitoring agent
      apt:
        name: prometheus-node-exporter
        state: present

    - name: Start monitoring agent
      service:
        name: prometheus-node-exporter
        state: started
```

---

## 8. How to Run a Playbook (Commands)

Basic command:

```bash
ansible-playbook playbook.yml -i inventory
```

Useful options:

- **Specify inventory**

  ```bash
  ansible-playbook playbook.yml -i inventory/hosts
  ```

- **Limit to a group or host**

  ```bash
  ansible-playbook playbook.yml -i inventory -l web
  ansible-playbook playbook.yml -i inventory -l web1
  ```

- **Dry run (check mode)** – see what would change:

  ```bash
  ansible-playbook playbook.yml -i inventory --check
  ```

- **Show file diffs:**

  ```bash
  ansible-playbook playbook.yml -i inventory --diff
  ```

- **Verbose output:**

  ```bash
  ansible-playbook playbook.yml -i inventory -v
  ansible-playbook playbook.yml -i inventory -vvv   # more details
  ```

- **Run only tasks with specific tag:**

  ```bash
  ansible-playbook playbook.yml -i inventory -t install
  ```

- **Skip tasks with a tag:**

  ```bash
  ansible-playbook playbook.yml -i inventory --skip-tags restart
  ```

- **Pass extra variables:**

  ```bash
  ansible-playbook playbook.yml -i inventory -e "app_version=v2.0 env=prod"
  ```

- **Ask for sudo password:**

  ```bash
  ansible-playbook playbook.yml -i inventory --ask-become-pass
  ```

- **Use vault encrypted file:**

  ```bash
  ansible-playbook playbook.yml -i inventory --ask-vault-pass
  ```

---

## 9. Variables in Playbooks (Simple View)

Variables help you avoid hard-coding values.

### 9.1 Declaring Variables in a Play

```yaml
---
- name: Example with variables
  hosts: web
  vars:
    app_version: v1.2.0
    http_port: 80
    app_user: www-data

  tasks:
    - name: Show version
      debug:
        msg: "Deploying version {{ app_version }} on port {{ http_port }}"
```

### 9.2 Variables from Inventory

`inventory/hosts`:

```ini
[web]
web1 ansible_host=10.0.1.10 app_env=production
web2 ansible_host=10.0.1.11 app_env=staging
```

Playbook:

```yaml
---
- name: Print environment
  hosts: web

  tasks:
    - name: Show environment
      debug:
        msg: "Environment is {{ app_env }}"
```

### 9.3 Variables from Files (`vars_files`)

```yaml
---
- name: Use vars from file
  hosts: web
  vars_files:
    - vars/common.yml
    - vars/web.yml

  tasks:
    - name: Show var from file
      debug:
        msg: "App name is {{ app_name }}"
```

`vars/common.yml`:

```yaml
app_name: my-awesome-app
app_version: v1.0.0
```

`vars/web.yml`:

```yaml
http_port: 80
worker_processes: 4
```

### 9.4 Using Facts (System Information)

Ansible automatically gathers facts about each host (OS, IP, CPU, etc.).

```yaml
---
- name: Use system facts
  hosts: all

  tasks:
    - name: Show OS
      debug:
        msg: "OS is {{ ansible_os_family }}"

    - name: Show IP address
      debug:
        msg: "IP is {{ ansible_default_ipv4.address }}"

    - name: Show hostname
      debug:
        msg: "Hostname is {{ ansible_hostname }}"
```

---

## 10. Handlers and Notify (Restart Services Only When Needed)

Handlers are used to do something **only when there is a change**, e.g., restart Nginx after config changes.

Example:

```yaml
---
- name: Configure Nginx
  hosts: web
  become: yes

  tasks:
    - name: Copy Nginx config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart Nginx

    - name: Copy SSL certificate
      copy:
        src: files/cert.pem
        dest: /etc/nginx/cert.pem
      notify: Restart Nginx

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

Flow:

- If template changes the file → `notify: Restart Nginx` is triggered.
- At the **end** of the play, handler `Restart Nginx` runs **only once** (even if notified multiple times).
- If nothing changed, handler does not run.

---

## 11. Conditions (`when`) and Loops (`loop`)

### 11.1 Conditions – `when`

```yaml
---
- name: Install package based on OS
  hosts: all
  become: yes

  tasks:
    - name: Install htop on Debian
      apt:
        name: htop
        state: present
      when: ansible_os_family == "Debian"

    - name: Install htop on RedHat
      yum:
        name: htop
        state: present
      when: ansible_os_family == "RedHat"

    - name: Install only in production
      apt:
        name: monitoring-agent
        state: present
      when: env == "prod"

    - name: Skip if already installed
      command: npm install
      when: not npm_installed | default(False)
```

### 11.2 Loops – `loop`

```yaml
---
- name: Install multiple packages
  hosts: web
  become: yes

  tasks:
    - name: Install packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - git
        - curl
        - vim

    - name: Create multiple directories
      file:
        path: "/tmp/{{ item }}"
        state: directory
      loop:
        - dir1
        - dir2
        - dir3

    - name: Create multiple users
      user:
        name: "{{ item.name }}"
        state: present
        groups: "{{ item.groups }}"
      loop:
        - { name: 'user1', groups: 'sudo' }
        - { name: 'user2', groups: 'docker' }
        - { name: 'user3', groups: 'deploy' }
```

---

## 12. Tags – Run Part of a Playbook

Add tags to tasks to run specific groups:

```yaml
---
- name: Setup web server
  hosts: web
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
      tags: install

    - name: Copy Nginx config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      tags: config

    - name: Copy SSL certificate
      copy:
        src: files/cert.pem
        dest: /etc/nginx/cert.pem
      tags: config

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
      tags: restart
```

Run commands:

```bash
# Run only install tasks
ansible-playbook web.yml -i inventory -t install

# Run only config tasks
ansible-playbook web.yml -i inventory -t config

# Run install and config, skip restart
ansible-playbook web.yml -i inventory -t install,config

# Skip restart tasks
ansible-playbook web.yml -i inventory --skip-tags restart
```

---

## 13. Templates (Use Variables in Files)

Jinja2 templates let you use variables inside files that Ansible copies.

`templates/app.conf.j2`:

```
server {
    listen {{ http_port }};
    server_name {{ app_domain }};
    
    location / {
        proxy_pass http://localhost:{{ app_port }};
    }
}
```

Playbook:

```yaml
---
- name: Deploy application config
  hosts: web
  become: yes

  vars:
    http_port: 80
    app_domain: myapp.com
    app_port: 3000

  tasks:
    - name: Copy app config from template
      template:
        src: templates/app.conf.j2
        dest: /etc/nginx/sites-available/myapp.conf
      notify: Restart Nginx

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

---

## 14. Roles (High-Level, Simple)

Roles help **organize big playbooks**.

Typical structure:

```text
project/
├── playbook.yml
├── inventory/
│   └── hosts
└── roles/
    ├── web/
    │   ├── tasks/
    │   │   └── main.yml
    │   ├── handlers/
    │   │   └── main.yml
    │   ├── templates/
    │   │   └── nginx.conf.j2
    │   ├── files/
    │   │   └── cert.pem
    │   └── vars/
    │       └── main.yml
    ├── db/
    │   ├── tasks/
    │   │   └── main.yml
    │   └── vars/
    │       └── main.yml
    └── monitoring/
        ├── tasks/
        │   └── main.yml
        └── templates/
            └── prometheus.yml.j2
```

Main playbook:

```yaml
---
- name: Configure app infrastructure
  hosts: all
  become: yes

  roles:
    - web
    - db
    - monitoring
```

`roles/web/tasks/main.yml`:

```yaml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Copy Nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart Nginx

- name: Start Nginx
  service:
    name: nginx
    state: started
    enabled: yes
```

Benefits:

- **Modular**: Each role has specific responsibility.
- **Reusable**: Use same role across multiple playbooks.
- **Maintainable**: Easy to find and update specific functionality.
- **Shareable**: Share roles with team or community.

---

## 15. Register and Use Task Output

Sometimes you need output from one task in another task.

```yaml
---
- name: Register and use output
  hosts: web

  tasks:
    - name: Check if nginx is installed
      command: which nginx
      register: nginx_check
      ignore_errors: yes

    - name: Install Nginx if not found
      apt:
        name: nginx
        state: present
      when: nginx_check.rc != 0  # rc = return code

    - name: Get current time
      command: date +%s
      register: current_time

    - name: Show current time
      debug:
        msg: "Current timestamp is {{ current_time.stdout }}"
```

---

## 16. Error Handling

### 16.1 Ignore Errors

```yaml
- name: Task that might fail
  command: /usr/bin/some_command_that_might_fail
  ignore_errors: yes
```

### 16.2 Custom Failure Condition

```yaml
- name: Check disk space
  shell: df -h / | grep -oP '\d+(?=%)'
  register: disk_usage
  failed_when: disk_usage.stdout | int > 90
```

### 16.3 Custom Success Condition

```yaml
- name: Verify deployment
  command: curl http://localhost:8080/health
  changed_when: False
  register: health_check
  until: health_check.rc == 0
  retries: 5
  delay: 10
```

---

## 17. Best Practices for Playbooks

- **Use meaningful names**
  - For plays and tasks: "Install Nginx", "Create deploy user".

- **Use variables**
  - Don't hard-code IPs, ports, versions.

- **Use handlers for restarts**
  - Restart services only when config changes.

- **Use tags for selective execution**
  - Easy to run specific parts of playbook.

- **Use `--check` and `--diff` first**
  - Always dry-run before production.

- **Use roles for big projects**
  - Easier to maintain and reuse.

- **Store in Git**
  - Version control for your infrastructure code.

- **Keep playbooks idempotent**
  - Running multiple times should not break things.

- **Add comments**
  - Explain complex logic or variable purposes.

- **Use descriptive variables names**
  - `app_port` instead of `port`.

---

## 18. Common Errors and Quick Fixes

| Error | Reason | Fix |
|-------|--------|-----|
| `YAMLException: mapping values` | Bad indentation, tabs | Use spaces, check indentation |
| `PLAY [web] => no hosts matched` | Group `web` not found in inventory | Check inventory group names |
| `FAILED! => {"msg": "Missing sudo"}` | Need root rights | Add `become: yes` |
| `module not found` | Wrong module name | Check with `ansible-doc -l` |
| `permission denied (publickey)` | SSH key or user wrong | Check `ansible_user` and key |
| `Undefined variable` | Variable not defined | Check variable names and spelling |
| `template error` | Wrong Jinja2 syntax | Check template file syntax |

---

## 19. Quick Cheatsheet

### Basic Playbook Structure

```yaml
---
- name: <Play name>
  hosts: <group>
  become: yes

  vars:
    key: value

  tasks:
    - name: <Task name>
      module:
        option: value
```

### Run Playbook

```bash
ansible-playbook playbook.yml -i inventory
ansible-playbook playbook.yml -i inventory -l web
ansible-playbook playbook.yml -i inventory --check --diff
ansible-playbook playbook.yml -i inventory -v
ansible-playbook playbook.yml -i inventory -t tag_name
```

### Use Variables

```yaml
vars:
  app_version: v1.0.0

tasks:
  - debug:
      msg: "Deploying {{ app_version }}"
```

### Use Handler

```yaml
tasks:
  - template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Restart Nginx

handlers:
  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```

### Use Condition

```yaml
tasks:
  - apt:
      name: nginx
    when: ansible_os_family == "Debian"
```

### Use Loop

```yaml
tasks:
  - apt:
      name: "{{ item }}"
    loop:
      - nginx
      - git
      - curl
```

### Use Tags

```yaml
tasks:
  - apt:
      name: nginx
    tags: install

# Run with tag
# ansible-playbook playbook.yml -t install
```

---

## 20. Complete Real-World Example

Full playbook deploying a Node.js application:

```yaml
---
- name: Deploy Node.js application
  hosts: app_servers
  become: yes

  vars:
    app_name: myapp
    app_repo: https://github.com/user/myapp.git
    app_version: v1.2.0
    app_dir: /var/www/{{ app_name }}
    app_port: 3000
    node_version: 16

  tasks:
    - name: Update system packages
      apt:
        update_cache: yes
        upgrade: dist
      tags: system

    - name: Install Node.js and npm
      apt:
        name:
          - nodejs
          - npm
        state: present
      tags: install

    - name: Create application user
      user:
        name: "{{ app_name }}"
        state: present
        createhome: yes
      tags: users

    - name: Clone application repository
      git:
        repo: "{{ app_repo }}"
        dest: "{{ app_dir }}"
        version: "{{ app_version }}"
      become_user: "{{ app_name }}"
      tags: deploy

    - name: Install dependencies
      npm:
        path: "{{ app_dir }}"
        state: present
      become_user: "{{ app_name }}"
      tags: deploy

    - name: Copy environment file
      template:
        src: templates/.env.j2
        dest: "{{ app_dir }}/.env"
        owner: "{{ app_name }}"
      tags: config

    - name: Copy systemd service file
      template:
        src: templates/myapp.service.j2
        dest: /etc/systemd/system/{{ app_name }}.service
      notify: Restart application
      tags: config

    - name: Enable and start application service
      systemd:
        name: "{{ app_name }}"
        daemon_reload: yes
        enabled: yes
        state: started
      tags: deploy

    - name: Install and configure Nginx as reverse proxy
      apt:
        name: nginx
        state: present
      tags: install

    - name: Copy Nginx config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/{{ app_name }}.conf
      notify: Restart Nginx
      tags: config

    - name: Enable Nginx site
      file:
        src: /etc/nginx/sites-available/{{ app_name }}.conf
        dest: /etc/nginx/sites-enabled/{{ app_name }}.conf
        state: link
      notify: Restart Nginx
      tags: config

    - name: Remove default Nginx site
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent
      notify: Restart Nginx
      tags: config

    - name: Verify application is running
      uri:
        url: http://localhost:{{ app_port }}/health
        method: GET
      register: app_health
      until: app_health.status == 200
      retries: 5
      delay: 10
      tags: verify

    - name: Display deployment summary
      debug:
        msg: |
          ===================================
          Application deployed successfully!
          Name: {{ app_name }}
          Version: {{ app_version }}
          Location: {{ app_dir }}
          Port: {{ app_port }}
          ===================================
      tags: always

  handlers:
    - name: Restart application
      systemd:
        name: "{{ app_name }}"
        state: restarted

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

---


Happy automating! 🚀
