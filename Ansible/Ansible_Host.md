
# Ansible Hosts File 

---

## 1. Problem Scenario

Imagine you are an **DevOps/Cloud engineer** managing **10-20 servers**.

You have:
- 3 web servers (web1, web2, web3)
- 2 database servers (db1, db2)
- 2 load balancers (lb1, lb2)
- Servers in different environments (production, testing, development)

**Without an inventory (hosts file)**, when you run Ansible, you would need to:
- Manually list each server IP address or hostname every time
- Remember which servers are in which group
- Type long commands with all server IPs every time you deploy

**Example** (without hosts file):
```bash
ansible-playbook deploy.yml -i 10.0.1.10,10.0.1.11,10.0.1.12,10.0.2.5,10.0.2.6
```

This is **repetitive, error-prone, and not scalable**.

**With a hosts file (Ansible Inventory)**, you can:
- Define all servers in one file
- Group servers logically (web, database, production, testing, etc.)
- Run commands simply by referring to group names

**Example** (with hosts file):
```bash
ansible-playbook deploy.yml -i inventory
# or just deploy to web servers
ansible-playbook deploy.yml -i inventory -l web
```

This is **clean, organized, and scalable**.

**The hosts file (Ansible inventory) is where you tell Ansible:**
- **"Here are all my servers"**
- **"Group them like this"**
- **"Connect using these credentials"**
- **"Use these special settings for each server"**

---

## 2. What Is the Ansible Hosts File?

### Simple Definition

The **Ansible hosts file** (also called **inventory file**) is a **simple text file** that contains:
- List of all your servers (IP addresses or hostnames)
- Grouped into logical groups (web, database, production, etc.)
- Connection details and variables for each server

### What Does It Look Like?

**Very simple example:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
web3 ansible_host=10.0.1.12

[database]
db1 ansible_host=10.0.2.5
db2 ansible_host=10.0.2.6

[loadbalancer]
lb1 ansible_host=10.0.3.1
lb2 ansible_host=10.0.3.2
```

**What each part means:**
- `[web]` → A **group** named "web"
- `web1` → A server name (you choose this)
- `ansible_host=10.0.1.10` → The actual IP address or hostname to connect to

---

## 3. Why Do We Use Ansible Hosts File?

### Main Reasons

1. **Organize Servers into Groups**
   - Instead of thinking about individual IPs, think about **groups** (web, database, testing, production)
   - Example: "Deploy to all web servers" instead of "Deploy to 10.0.1.10, 10.0.1.11, 10.0.1.12"

2. **Reusability**
   - Define servers once in the hosts file
   - Use them again and again without re-typing

3. **Easy to Manage**
   - Add, remove, or modify servers in **one place**
   - All playbooks automatically use the updated list

4. **Scale Easily**
   - Start with 5 servers, grow to 50 servers
   - Just add new entries to the hosts file

5. **Run Commands on Specific Groups**
   - Run playbook on only web servers: `ansible-playbook deploy.yml -l web`
   - Run on only production: `ansible-playbook deploy.yml -l production`

6. **Store Connection Details**
   - SSH username, password, SSH key, port, etc.
   - All in one place for each server or group

7. **Environment Separation**
   - Separate files/sections for production, testing, development
   - Reduce risk of accidentally modifying production while testing

---

## 4. Benefits of Using Ansible Hosts File

| Benefit | How It Helps |
|---------|------------|
| **Organization** | Group related servers together logically |
| **Reusability** | Write once, use many times |
| **Scalability** | Easily add/remove servers |
| **Safety** | Less chance of manual errors |
| **Consistency** | All playbooks use same server definitions |
| **Automation** | Run complex deployments with simple commands |
| **Documentation** | Hosts file shows your infrastructure layout |
| **Environment Control** | Separate prod/test/dev configurations |
| **Reduced Typing** | No need to remember IPs or hostnames |

---

## 5. Ansible Hosts File Structure (Architecture)

### Overall Structure

```
Ansible Hosts File (inventory)
│
├── Group 1: [web]
│   ├── Server 1 (web1)
│   ├── Server 2 (web2)
│   └── Server 3 (web3)
│
├── Group 2: [database]
│   ├── Server 1 (db1)
│   └── Server 2 (db2)
│
└── Group 3: [production]
    ├── Server 1 (prod-app1)
    ├── Server 2 (prod-app2)
    └── Server 3 (prod-db1)
```

### Components Breakdown

1. **Group Names** (inside square brackets)
   - `[web]`, `[database]`, `[production]`, `[development]`
   - You choose meaningful names
   - Used to target servers in playbooks

2. **Server Entries** (inside groups)
   - `web1`, `web2` → Friendly names you choose
   - `ansible_host=10.0.1.10` → IP address or hostname to actually connect to

3. **Server Variables** (connection details)
   - `ansible_user=ubuntu` → SSH username
   - `ansible_ssh_private_key_file=/path/to/key.pem` → SSH key file
   - `ansible_port=22` → SSH port
   - `ansible_password=mypassword` → SSH password (not recommended; use SSH keys)

---

## 6. File Location – Where Is the Hosts File?

### Default Locations (Ansible Looks Here)

Ansible automatically looks for the inventory file in these locations (in order):

1. **Specified with `-i` flag** (command line)
   ```bash
   ansible-playbook deploy.yml -i /path/to/my/inventory
   ```
   This is the **most common and recommended** way.

2. **`ANSIBLE_INVENTORY` environment variable**
   ```bash
   export ANSIBLE_INVENTORY=/path/to/inventory
   ansible-playbook deploy.yml
   ```

3. **`~/ansible.cfg`** (Ansible config file, your home directory)
   - Set default inventory location here

4. **`/etc/ansible/hosts`** (System default location)
   - Default if nothing else is specified
   - Requires root/sudo to edit

### Recommended Setup

**Create a dedicated inventory folder in your project:**

```
my-ansible-project/
├── inventory/
│   ├── hosts                    (or inventory)
│   ├── production
│   ├── staging
│   └── development
├── playbooks/
│   ├── deploy.yml
│   ├── install.yml
│   └── configure.yml
├── roles/
│   ├── web/
│   └── database/
└── ansible.cfg
```

**Run playbooks with explicit inventory path:**
```bash
ansible-playbook playbooks/deploy.yml -i inventory/hosts
# or
ansible-playbook playbooks/deploy.yml -i inventory/production
```

### Quick Summary: File Paths

| Location | Type | Example |
|----------|------|---------|
| Command line (recommended) | Relative/Absolute | `-i inventory/hosts` or `-i ./hosts` |
| Home directory | Default config | `~/.ansible/hosts` (if configured) |
| System default | Built-in | `/etc/ansible/hosts` |
| Project folder | Best practice | `./inventory/` folder in your project |

---

## 7. What to Edit in the Hosts File

### 7.1 Server Names and IP Addresses

**What it looks like:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
```

**What to edit:**
- `web1`, `web2` → Change to meaningful names for your servers
- `10.0.1.10`, `10.0.1.11` → Change to your actual server IPs or hostnames

**Example:**
```ini
[web]
prod-web-app1 ansible_host=192.168.1.100
prod-web-app2 ansible_host=192.168.1.101
staging-web1 ansible_host=192.168.1.200
```

---

### 7.2 Connection Settings (ansible_user, ansible_port, etc.)

**What it looks like:**
```ini
[web]
web1 ansible_host=10.0.1.10 ansible_user=ubuntu ansible_port=22
```

**Common settings to edit:**

| Setting | Default | What to Change To |
|---------|---------|-------------------|
| `ansible_user` | root | Your SSH username (ubuntu, ec2-user, admin, etc.) |
| `ansible_port` | 22 | Custom SSH port if not default |
| `ansible_ssh_private_key_file` | ~/.ssh/id_rsa | Path to your SSH key file |
| `ansible_password` | (none) | Password (NOT recommended; use SSH keys) |
| `ansible_become` | (none) | Set to true if you need sudo |
| `ansible_become_method` | sudo | Use sudo or su for privilege escalation |
| `ansible_become_user` | root | Which user to become after login |

**Example – What to Edit:**
```ini
[web]
# For Ubuntu servers (use ubuntu user, SSH key)
web1 ansible_host=10.0.1.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/aws-key.pem

# For RHEL/CentOS servers (use ec2-user)
web2 ansible_host=10.0.1.11 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/aws-key.pem

# For servers that need sudo (become root)
db1 ansible_host=10.0.2.5 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/aws-key.pem ansible_become=true ansible_become_user=root
```

---

### 7.3 Custom Variables

**What it looks like:**
```ini
[web]
web1 ansible_host=10.0.1.10 app_env=production
```

**What to edit:**
- Add custom variables specific to each server
- These variables can be used in playbooks

**Example – What to Edit:**
```ini
[web]
web1 ansible_host=10.0.1.10 app_env=production nginx_worker_processes=4
web2 ansible_host=10.0.1.11 app_env=staging nginx_worker_processes=2

[database]
db1 ansible_host=10.0.2.5 mysql_port=3306 mysql_max_connections=500
```

Then in your playbook, use these variables:
```yaml
- name: Configure Nginx
  hosts: web
  tasks:
    - name: Set worker processes
      lineinfile:
        path: /etc/nginx/nginx.conf
        regexp: '^worker_processes'
        line: "worker_processes {{ nginx_worker_processes }};"
```

---

### 7.4 Group Variables (apply to entire group)

**What it looks like:**
```ini
[web:vars]
app_version=v2.1
environment=production
```

**What to edit:**
- Add variables that apply to **all servers in the group**
- More efficient than repeating same variable for each server

**Example – What to Edit:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/aws-key.pem
ansible_become=true
app_version=v2.1
environment=production
nginx_worker_processes=4

[database]
db1 ansible_host=10.0.2.5
db2 ansible_host=10.0.2.6

[database:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/aws-key.pem
mysql_port=3306
mysql_max_connections=500
```

---

### 7.5 Parent Groups (grouping groups)

**What it looks like:**
```ini
[prod_servers:children]
web
database

[dev_servers:children]
web-dev
db-dev
```

**What to edit:**
- Create groups that contain other groups
- Use `[groupname:children]` to include other groups

**Example – What to Edit:**
```ini
# Production servers
[prod-web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11

[prod-db]
db1 ansible_host=10.0.2.5

# Development servers
[dev-web]
dev-web1 ansible_host=10.0.10.10

[dev-db]
dev-db1 ansible_host=10.0.20.5

# Parent groups
[production:children]
prod-web
prod-db

[development:children]
dev-web
dev-db

# All servers
[all_environments:children]
production
development
```

Now you can run:
```bash
ansible-playbook deploy.yml -l production     # All production servers
ansible-playbook deploy.yml -l dev-web        # Only dev web servers
ansible-playbook deploy.yml -l all_environments  # All servers
```

---

## 8. How to Edit the Hosts File

### Step 1: Open the File in a Text Editor

**On Linux/Mac:**
```bash
# If you have a local inventory file in your project
nano inventory/hosts
# or
vim inventory/hosts

# If using system default
sudo nano /etc/ansible/hosts
```

**On Windows:**
- Use Notepad, VS Code, or any text editor
- Navigate to the file location and open it

### Step 2: Add or Modify Entries

**Start with this template:**
```ini
# Web Servers
[web]
web1 ansible_host=10.0.1.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/key.pem
web2 ansible_host=10.0.1.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/key.pem

# Database Servers
[database]
db1 ansible_host=10.0.2.5 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/key.pem

# Production group (contains web and database)
[production:children]
web
database
```

### Step 3: Save the File

- **Nano**: Press `Ctrl + X`, then `Y`, then `Enter`
- **Vim**: Press `Esc`, type `:wq`, then `Enter`
- **Text Editor**: Use `Ctrl + S` or File → Save

### Step 4: Verify Syntax (Optional but Recommended)

```bash
ansible-inventory -i inventory/hosts --list
# or
ansible-inventory -i inventory/hosts --graph
```

This shows all your hosts and groups without errors.

---

## 9. Common Editing Scenarios

### Scenario 1: Add a New Web Server

**Before:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
```

**After:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
web3 ansible_host=10.0.1.12
```

---

### Scenario 2: Change SSH User

**Before:**
```ini
[web]
web1 ansible_host=10.0.1.10 ansible_user=root
```

**After:**
```ini
[web]
web1 ansible_host=10.0.1.10 ansible_user=ubuntu
```

---

### Scenario 3: Use SSH Key Instead of Password

**Before:**
```ini
[database]
db1 ansible_host=10.0.2.5 ansible_user=ubuntu ansible_password=mypassword
```

**After:**
```ini
[database]
db1 ansible_host=10.0.2.5 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/db-key.pem
```

---

### Scenario 4: Separate Production and Staging

**Before:**
```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
```

**After:**
```ini
[prod-web]
web1 ansible_host=10.0.1.10

[staging-web]
web2 ansible_host=10.0.1.11

[production:children]
prod-web

[staging:children]
staging-web
```

---

## 10. Complete Practical Example

Here's a **complete, ready-to-use example** you can copy and modify:

```ini
# ============================================
# ANSIBLE INVENTORY FILE (HOSTS)
# ============================================

# === WEB SERVERS (PRODUCTION) ===
[prod-web]
prod-web1 ansible_host=10.0.1.10
prod-web2 ansible_host=10.0.1.11
prod-web3 ansible_host=10.0.1.12

# === WEB SERVERS (STAGING) ===
[staging-web]
staging-web1 ansible_host=10.0.1.20

# === DATABASE SERVERS (PRODUCTION) ===
[prod-db]
prod-db1 ansible_host=10.0.2.5 mysql_master=true
prod-db2 ansible_host=10.0.2.6 mysql_master=false

# === DATABASE SERVERS (STAGING) ===
[staging-db]
staging-db1 ansible_host=10.0.2.15

# === LOAD BALANCERS ===
[lb]
lb1 ansible_host=10.0.3.1
lb2 ansible_host=10.0.3.2

# === GROUP ALL WEB SERVERS ===
[web:children]
prod-web
staging-web

# === GROUP ALL DATABASE SERVERS ===
[database:children]
prod-db
staging-db

# === GROUP ALL PRODUCTION SERVERS ===
[production:children]
prod-web
prod-db

# === GROUP ALL STAGING SERVERS ===
[staging:children]
staging-web
staging-db

# === GROUP ALL SERVERS ===
[all_servers:children]
production
staging
lb

# === CONNECTION SETTINGS FOR PRODUCTION ===
[production:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/production-key.pem
ansible_port=22
ansible_become=true
ansible_become_method=sudo
environment=production

# === CONNECTION SETTINGS FOR STAGING ===
[staging:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/staging-key.pem
ansible_port=22
ansible_become=true
ansible_become_method=sudo
environment=staging

# === CONNECTION SETTINGS FOR LOAD BALANCERS ===
[lb:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/lb-key.pem
ansible_port=22

# === CUSTOM VARIABLES ===
[prod-web:vars]
app_version=v2.1.0
max_connections=1000
nginx_worker_processes=8

[staging-web:vars]
app_version=v2.0.0
max_connections=500
nginx_worker_processes=4

[prod-db:vars]
mysql_max_connections=500
mysql_innodb_buffer_pool_size=8G

[staging-db:vars]
mysql_max_connections=200
mysql_innodb_buffer_pool_size=2G
```

---

## 11. How to Use This Hosts File

### Run Playbook on All Production Servers
```bash
ansible-playbook deploy.yml -i inventory -l production
```

### Run Playbook on Only Web Servers
```bash
ansible-playbook deploy.yml -i inventory -l web
```

### Run Playbook on Production Web Servers Only
```bash
ansible-playbook deploy.yml -i inventory -l prod-web
```

### Run Playbook on Database Servers
```bash
ansible-playbook deploy.yml -i inventory -l database
```

### Run on All Servers
```bash
ansible-playbook deploy.yml -i inventory
```

### Check Connectivity to All Hosts
```bash
ansible all -i inventory -m ping
```

### Check Connectivity to Production Only
```bash
ansible production -i inventory -m ping
```

---

## 12. File Format Options

### Format 1: INI Format (Simple and Common)

```ini
[web]
web1 ansible_host=10.0.1.10
web2 ansible_host=10.0.1.11
```

**Pros**: Simple, human-readable, good for beginners
**Cons**: Limited for complex setups

---

### Format 2: YAML Format (More Powerful)

```yaml
all:
  children:
    web:
      hosts:
        web1:
          ansible_host: 10.0.1.10
        web2:
          ansible_host: 10.0.1.11
    database:
      hosts:
        db1:
          ansible_host: 10.0.2.5
  vars:
    ansible_user: ubuntu
    ansible_ssh_private_key_file: ~/.ssh/key.pem
```

**Pros**: More structured, supports complex hierarchies, good for large infrastructure
**Cons**: More verbose, requires YAML knowledge

---

### Recommendation for Beginners

**Start with INI format** (shown above). It is simpler and easier to understand. Once you get comfortable, you can upgrade to YAML format.

---

## 13. Best Practices

### Do's ✅

- ✅ **Use meaningful group names** (web, database, production, staging)
- ✅ **Use SSH keys instead of passwords** (more secure)
- ✅ **Separate inventory files for different environments** (production.ini, staging.ini)
- ✅ **Use group variables** to avoid repeating same settings
- ✅ **Store inventory in version control** (Git) but NOT sensitive data like passwords
- ✅ **Use meaningful server names** (prod-web1, staging-db2)
- ✅ **Document your inventory** with comments starting with `#`

---

### Don'ts ❌

- ❌ **Don't hardcode passwords** in inventory file (security risk)
- ❌ **Don't use generic names** like "server1", "server2" (hard to identify)
- ❌ **Don't forget to update inventory** when adding/removing servers
- ❌ **Don't store inventory with plain-text SSH keys** (use Ansible Vault)
- ❌ **Don't use special characters** in server or group names that are not allowed in shell
- ❌ **Don't edit inventory during a playbook run** (can cause unexpected behavior)

---

## 14. Security Best Practices

### Protecting Your Inventory

**Problem**: Inventory contains sensitive info (IPs, SSH key paths, sometimes passwords)

**Solution 1: Use Ansible Vault**
```bash
# Encrypt the entire inventory file
ansible-vault encrypt inventory/hosts

# Run playbook with encrypted inventory
ansible-playbook deploy.yml -i inventory/hosts --ask-vault-pass
```

**Solution 2: Use .gitignore**
```bash
# In your project's .gitignore file
echo "inventory/hosts" >> .gitignore
echo "*.key" >> .gitignore
echo "*.pem" >> .gitignore
```

**Solution 3: Store Sensitive Data Separately**
```ini
# inventory/hosts (commit to Git)
[web]
web1 ansible_host=10.0.1.10

# inventory/group_vars/web.yml (NOT committed to Git)
ansible_user: ubuntu
ansible_ssh_private_key_file: ~/.ssh/production-key.pem
```

---

## 15. Troubleshooting Common Issues

### Issue 1: "Host not found" or Connection Refused

**Check**:
1. Is the IP/hostname correct? `ping 10.0.1.10`
2. Is SSH port correct? (default 22)
3. Is SSH key path correct? `ls ~/.ssh/key.pem`
4. Do you have SSH access to this server?

**Test**:
```bash
ansible -i inventory -m ping web1
```

---

### Issue 2: "Permission denied" or Authentication Failed

**Check**:
1. Is the SSH user correct?
2. Is the SSH key correct?
3. Does the key have correct permissions? `chmod 600 ~/.ssh/key.pem`
4. Is the key added to SSH agent? `ssh-add ~/.ssh/key.pem`

**Test**:
```bash
ssh -i ~/.ssh/key.pem ubuntu@10.0.1.10
```

---

### Issue 3: "No hosts matched"

**Check**:
1. Is the group name spelled correctly?
2. Is the host added to that group?
3. Run `ansible-inventory -i inventory --list` to see all groups

**Fix**:
```bash
# List all groups
ansible-inventory -i inventory --graph

# Check specific group
ansible -i inventory -m debug -a "msg=test" -l web
```

---

## 16. Summary

| Aspect | What to Know |
|--------|-------------|
| **What** | A text file listing all your servers and groups |
| **Why** | Organize servers, reuse configs, scale easily, reduce errors |
| **Location** | Anywhere, but best in project folder (inventory/) |
| **Format** | INI (simple) or YAML (powerful) |
| **What to edit** | Server names, IPs, groups, SSH settings, variables |
| **File name** | Usually "hosts" or "inventory" (you choose) |
| **How to edit** | Any text editor (nano, vim, VS Code, Notepad) |
| **Best practice** | Use SSH keys, separate prod/staging/dev, document well |

---

## 17. Quick Start Template

Save this as `inventory/hosts` and modify for your servers:

```ini
# === YOUR SERVERS ===
[web]
web1 ansible_host=YOUR_IP_HERE ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/YOUR_KEY.pem
web2 ansible_host=YOUR_IP_HERE ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/YOUR_KEY.pem

[database]
db1 ansible_host=YOUR_IP_HERE ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/YOUR_KEY.pem

# === GROUP SETTINGS ===
[web:vars]
ansible_become=true
environment=production

[database:vars]
ansible_become=true
environment=production
```

Replace:
- `YOUR_IP_HERE` → Your actual server IPs
- `ubuntu` → Your SSH username
- `YOUR_KEY.pem` → Your actual SSH key file path

---

