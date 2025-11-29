# Ansible Inventory Setup for Mixed Ubuntu & Amazon Linux Servers

Managing servers with different operating systems in Ansible is common — especially when working with both **Ubuntu** and **Amazon Linux** EC2 instances.  
Each OS uses a different default SSH user, so defining the correct user in your inventory is important.

This guide explains how to configure your Ansible inventory when you have multiple OS types.

---

## 🔹 Why This Matters

Ansible connects to servers using SSH.  
If the username is wrong, Ansible will fail with:

```
UNREACHABLE! => Failed to authenticate or connect
```

To avoid this, set the correct SSH user per server or per group.

---

## 🔹 Default EC2 SSH Users

| OS Type        | Default SSH User |
|----------------|------------------|
| Ubuntu         | ubuntu           |
| Amazon Linux   | ec2-user         |

---

# ✅ Recommended Inventory Structure

Create a file named **inventory.ini**:

```
[ubuntu_servers]
ubuntu1 ansible_host=18.201.102.34
ubuntu2 ansible_host=54.154.36.177

[amazon_servers]
amazon1 ansible_host=3.92.44.12
amazon2 ansible_host=44.201.10.55

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key=/home/ubuntu/keys/batch-8-ansible.pem

[ubuntu_servers:vars]
ansible_user=ubuntu

[amazon_servers:vars]
ansible_user=ec2-user
```

---

# 📝 Explanation

### **[ubuntu_servers] / [amazon_servers]**
Groups servers by OS type.  
Clean and scalable.

---

### **[all:vars]**
Variables applied to every host.

- `ansible_python_interpreter` → Python path on the server  
- `ansible_ssh_private_key` → location of `.pem` key  

---

### **Group Variables**
Set SSH user depending on OS:

```
[ubuntu_servers:vars]
ansible_user=ubuntu
```

```
[amazon_servers:vars]
ansible_user=ec2-user
```

---

# 🔥 Alternative: Per-Host User Definition

```
[servers]
server1 ansible_host=18.201.102.34 ansible_user=ubuntu
server2 ansible_host=54.154.36.177 ansible_user=ubuntu
server3 ansible_host=3.92.44.12 ansible_user=ec2-user
server4 ansible_host=44.201.10.55 ansible_user=ec2-user
```

Good for small setups.

---

# 🚀 Test Your Inventory

Run:

```
ansible all -i inventory.ini -m ping
```

Expected output:

```
server1 | SUCCESS => pong
server2 | SUCCESS => pong
...
```

If it fails, check:
- Correct SSH user  
- PEM key path  
- Security group inbound rules  
- Public IP  

---


