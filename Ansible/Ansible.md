# Ansible

## 1. Problem Scenario

Imagine you are managing many servers (10, 20, or even 100+).  
You need to install software, update configurations, and deploy applications on all of them.

If you do this manually:
- You SSH into each server one by one
- You run the same commands again and again
- You may forget some steps or make mistakes
- Some servers become different from others (inconsistent)
- It becomes very slow and hard to manage as servers grow

This is not scalable or reliable.

We need a tool that:
- Automates these repeated tasks
- Keeps all servers consistent
- Is easy to read, write, and maintain

That tool is **Ansible**.

---

## 2. What is Ansible?

Ansible is an **open-source automation tool** used for:

- Configuration management
- Application deployment
- Orchestration and server automation

In simple words:

> Ansible lets you write instructions in YAML and run them on many servers at once, using SSH, without installing any agent on those servers.

---

## 3. Why Do We Use Ansible?

We use Ansible to:

- Avoid manual and repetitive work on multiple servers
- Keep all servers in a consistent state
- Reduce human errors and typos
- Save time and effort, especially at scale
- Document our infrastructure as code (playbooks in Git)
- Support CI/CD and automated deployments

Some common use cases:
- Installing and configuring web servers (Nginx, Apache)
- Managing users, SSH keys, and permissions
- Deploying applications (Java, Python, Node.js, etc.)
- Configuring databases, load balancers, and monitoring agents
- Managing cloud resources (AWS, GCP, Azure) using Ansible modules

---

## 4. Benefits of Ansible

- **Simple and human-readable**
  - Uses YAML for playbooks
  - Easy to read, write, and understand

- **Agentless**
  - No agent/software needed on client machines
  - Uses SSH (Linux/Unix) or WinRM (Windows) for communication

- **Idempotent**
  - Safe to run the same playbook multiple times
  - Applies changes only when needed

- **Scalable**
  - Manage a few or hundreds of servers from a single control node

- **Repeatable and version-controlled**
  - Playbooks can be stored in Git
  - Same configuration can be applied to dev, test, and prod

- **Rich module ecosystem**
  - Many built-in modules for packages, services, files, users, cloud providers, etc.

---

## 5. Ansible Architecture

### 5.1 Key Components

- **Control Node**
  - The machine where Ansible is installed
  - You run `ansible` and `ansible-playbook` commands from here

- **Managed Nodes (Client/Target Machines)**
  - Servers or devices that Ansible manages
  - No agent required, only SSH or WinRM access

- **Inventory**
  - A file listing all your servers and their groups
  - Example groups: `[web]`, `[db]`, `[prod]`, `[dev]`

- **Playbooks (YAML Files)**
  - Main files where you define what Ansible should do
  - Contain plays and tasks

- **Tasks**
  - Individual actions Ansible will perform
  - Example: install a package, start a service, copy a file

- **Modules**
  - Pre-built small programs used by Ansible to perform tasks
  - Examples: `apt`, `yum`, `service`, `copy`, `user`, `template`

- **Handlers**
  - Special tasks that run only when notified
  - Common use: restart a service only if a config file changed

- **Roles**
  - A structured way to organize playbooks for reuse
  - Useful for larger projects

---

### 5.2 How Ansible Works (High Level)

1. You define your servers in an **inventory file**.
2. You write a **playbook** in YAML describing the desired state.
3. From the **control node**, you run the playbook using `ansible-playbook`.
4. Ansible connects to the **managed nodes** using SSH or WinRM.
5. Ansible runs each **task** using the appropriate **module**.
6. Ansible reports which tasks changed something and which were already OK.

---

