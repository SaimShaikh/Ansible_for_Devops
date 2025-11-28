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

# Ansible Core Concepts (Interview-Ready Guide)

## 1. Control Node
- The machine where Ansible is installed.
- You execute `ansible` and `ansible-playbook` commands from here.
- Uses SSH (Linux) or WinRM (Windows) to communicate.
- No heavy resources required.
- Follows a **push-based model**: control node pushes configurations to targets.

👉 **Key Point:** Control node is the *brain* of Ansible.

---

## 2. Managed Nodes (Target Machines)
- Servers that Ansible manages: web servers, DB servers, app servers, etc.
- **Agentless** — no installation required.
- Needs only:
  - SSH access  
  - Python on Linux (default)
- Can manage hundreds or thousands of servers.

👉 **Key Point:** Managed nodes stay clean — no agents or daemons.

---

## 3. Inventory
- A file (`hosts` or `inventory.ini`) listing all servers.
- Supports grouping:  
  - `[web]`  
  - `[db]`  
  - `[prod]`  
  - `[dev]`
- You can assign variables at group or host level.
- Supports **static** and **dynamic** inventories (AWS, Azure, GCP).

👉 **Key Point:** Inventory is the *source of truth* for your infrastructure.

---

## 4. Playbooks (YAML Files)
- Main files where automation is defined.
- Contain:
  - **Plays** – which hosts to run on.
  - **Tasks** – actions to perform.
- Written in YAML.
- Idempotent — multiple runs produce the same result.

👉 **Key Point:** Playbooks describe the *desired state* of the system.

---

## 5. Tasks
- Smallest unit of work in Ansible.
- Each task uses a module.
- Examples:
  - Installing a package
  - Starting a service
  - Copying a file
  - Creating a user
- Tasks run sequentially and are idempotent.

👉 **Key Point:** Tasks are declarative — you define *what*, Ansible decides *how*.

---

## 6. Modules
- Pre-built programs Ansible uses to execute tasks.
- 1500+ built-in modules:
  - OS modules: `apt`, `yum`, `file`, `service`
  - Cloud modules: AWS, Azure, GCP
  - Docker, Kubernetes modules
  - Networking modules
- You can create custom modules in Python.

👉 **Key Point:** Modules give Ansible its power and extensibility.

---

## 7. Handlers
- Special tasks triggered by `notify`.
- Run **only when a task reports a change**.
- Common use cases:
  - Restarting a service
  - Reloading config files
  - Renewing caches

👉 **Key Point:** Handlers prevent unnecessary restarts and help maintain stability.

---

## 8. Roles
- A structured, reusable way to organize automation.
- Each role contains:
  - `tasks/`
  - `handlers/`
  - `templates/`
  - `files/`
  - `vars/`
  - `defaults/`
- Helps maintain clean, modular, production-level code.
- Roles can be imported/downloaded via **Ansible Galaxy**.




---




### 5.2 How Ansible Works (High Level)

1. You define your servers in an **inventory file**.
2. You write a **playbook** in YAML describing the desired state.
3. From the **control node**, you run the playbook using `ansible-playbook`.
4. Ansible connects to the **managed nodes** using SSH or WinRM.
5. Ansible runs each **task** using the appropriate **module**.
6. Ansible reports which tasks changed something and which were already OK.

---

