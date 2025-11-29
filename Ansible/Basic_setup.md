<img width="2239" height="1047" alt="image" src="https://github.com/user-attachments/assets/5a4da965-3f2a-4842-bb06-dba4778d065d" />

# Basic Ansible Practical Lab on AWS EC2

This README explains a **basic practical setup** of Ansible using **4 EC2 instances**:

- 1 **Master node** (Ansible control node)
- 3 **Slave nodes** (managed nodes)
- All EC2 instances use the **same `.pem` key**

---

## ✅ Prerequisites

- AWS account
- 4 EC2 instances (Ubuntu preferred):
  - 1 master node
  - 3 slave nodes
- All 4 EC2 instances launched using the **same key pair** → `mykey.pem`
- Security group:
  - Allow SSH (port 22) from your IP to all 4 instances

---

## 1️⃣ Setup Overview

- Install **Ansible only on the master node**
- Slave nodes do **not** need Ansible
- Copy your `.pem` key from your **local machine** to the master node
- Configure Ansible inventory on the master node
- And the correct way to handle host key verification for many servers
- Test Ansible connection using `ping` module

---

## 2️⃣ Install Ansible on Master Node

SSH into the **master node** from your local machine:

```bash
ssh -i mykey.pem ubuntu@<MASTER_PUBLIC_IP>
```

Update package index and install Ansible:

```bash

 sudo apt update
 sudo apt install software-properties-common
 sudo add-apt-repository --yes --update ppa:ansible/ansible
 sudo apt install ansible
```

Verify:

```bash
ansible --version
```

---

## 3️⃣ Create a Folder for Private Key on Master Node

On the **master node**, create a directory to store your private key:

```bash
mkdir -p /home/ubuntu/keys
```

---

## 4️⃣ Transfer `.pem` Key from Local to Master Node

From your **local terminal** (not inside SSH):

```bash
scp -i mykey.pem mykey.pem ubuntu@<MASTER_PUBLIC_IP>:/home/ubuntu/keys/
```

This command:

- Uses `mykey.pem` locally to connect to the master node
- Copies `mykey.pem` into `/home/ubuntu/keys/` on the master node

Now SSH back into the master (if not already):

```bash
ssh -i mykey.pem ubuntu@<MASTER_PUBLIC_IP>
```

Set proper permissions on the key **on the master node**:

```bash
chmod 600 /home/ubuntu/keys/mykey.pem
```

---

## 5️⃣ Configure Ansible Inventory (`/etc/ansible/hosts`)

Open the Ansible hosts file on the **master node**:

```bash
sudo vim /etc/ansible/hosts
```

Add the following content (replace IPs with your **slave instances’ private IPs**):

```ini
[servers]
server1 ansible_host=10.0.0.11
server2 ansible_host=10.0.0.12
server3 ansible_host=10.0.0.13

[servers:vars]
ansible_user=ubuntu
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=/home/ubuntu/keys/mykey.pem
```

> 🔁 Replace `10.0.0.11`, `10.0.0.12`, `10.0.0.13` with your actual **slave node** private IPs.

Save and exit:

- Press `Esc`
- Type:
  ```vim
  :wq
  ```

---

## 6️⃣ Test Ansible Connectivity

From the **master node**, run:

```bash
ansible servers -m ping
```

Expected output (for each server):

```text
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
server2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
server3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

If you see `SUCCESS` with `ping: "pong"` for all three servers,  
✅ your Ansible basic practical setup is working correctly.

---
If You receives any Error like 
```bash
[ERROR]: Task failed: Failed to connect to the host via ssh: Host key verification failed.
Origin: <adhoc 'ping' task>

{'action': 'ping', 'args': {}, 'timeout': 0, 'async_val': 0, 'poll': 15}

server-1 | UNREACHABLE! => {
    "changed": false,
    "msg": "Task failed: Failed to connect to the host via ssh: Host key verification failed.",
    "unreachable": true
}
server-2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Task failed: Failed to connect to the host via ssh: Host key verification failed.",
    "unreachable": true
}

```
## Do This 
```bash
vim servers.txt
In This add Your Servers IPs for eg like this 
13.235.90.15
13.201.134.75
10.0.0.13
```

## Run ssh-keyscan
```bash
for IP in $(cat servers.txt); do
    ssh-keyscan -H "$IP" >> ~/.ssh/known_hosts
done

and Tst again
ansible servers -m ping

```
## Output 
<img width="908" height="298" alt="Screenshot 2025-11-28 at 10 18 44 PM (2)" src="https://github.com/user-attachments/assets/e0c77441-fda8-4638-8c02-bec8aeb00f4a" />
<img width="2195" height="1218" alt="image" src="https://github.com/user-attachments/assets/62b33e53-e697-4b30-8bce-d71e130788e8" />


