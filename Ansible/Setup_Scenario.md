
# Managing Multiple Ansible Nodes With Different SSH Keys

When working with Ansible, you may have multiple managed nodes (servers) and each one may use a **different SSH key**.  
Ansible can still manage all of them easily — you just need to map each server to its own key inside the inventory file.

This guide explains exactly how to do that.

---

## ✅ Architecture Overview
- **1 Control Node (Master Node)**  
  Where Ansible is installed and all commands/playbooks are executed.

- **3 Managed Nodes (Slave Nodes)**  
  Servers you want to automate using Ansible.

- **Connection Method:** SSH  
  No agent required — Ansible is completely agentless.

---

# ⭐ Case 1: Using the Same SSH Key for All Servers (Recommended)

### Step 1: Generate SSH Key on the Master Node
```bash
ssh-keygen
```

### Step 2: Copy the Key to Each Server
```bash
ssh-copy-id ubuntu@server1
ssh-copy-id ubuntu@server2
ssh-copy-id ubuntu@server3
```

### Step 3: Inventory File Example
```ini
[web]
server1 ansible_host=IP1
server2 ansible_host=IP2
server3 ansible_host=IP3
```

### Step 4: Test Connection
```bash
ansible all -m ping
```

---

# ⭐ Case 2: Each Server Uses a DIFFERENT SSH Key

Ansible supports this easily by defining unique key paths per host.

---

## ✔️ Step 1: Store SSH Keys on Master Node
Example directory structure:
```
/home/ansible/keys/server1.pem
/home/ansible/keys/server2.pem
/home/ansible/keys/server3.pem
```

Secure permissions:
```bash
chmod 600 /home/ansible/keys/*.pem
```

---

## ✔️ Step 2: Add SSH Key Paths in `inventory.ini`
```ini
[web]
server1 ansible_host=IP1 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ansible/keys/server1.pem
server2 ansible_host=IP2 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ansible/keys/server2.pem
server3 ansible_host=IP3 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ansible/keys/server3.pem
```

---

## ✔️ Step 3: Test All Connections
```bash
ansible web -m ping
```

If everything is correct, all 3 servers will respond:
```
pong
```

---

# 📂 Optional: Using Group Variables for Cleaner Inventory

Create file:
```
group_vars/web.yml
```

Add:
```yaml
ansible_user: ubuntu
ansible_port: 22
```

Now your inventory becomes cleaner:
```ini
[web]
server1 ansible_host=IP1 ansible_ssh_private_key_file=/home/ansible/keys/server1.pem
server2 ansible_host=IP2 ansible_ssh_private_key_file=/home/ansible/keys/server2.pem
server3 ansible_host=IP3 ansible_ssh_private_key_file=/home/ansible/keys/server3.pem
```

---
