## Q1. What is configuration management?

Ansible is an open-source automation platform used to configure systems, deploy applications, and orchestrate IT workflows across servers, containers, network gear, and cloud services.

- Automates configuration, application deployment, and orchestration across servers, containers, networks, and clouds.
- Uses human-readable YAML playbooks to declare desired state, with tasks organized into reusable roles.
- Easy to adopt (plain YAML), extensive modules/collections, and smooth CI/CD integration.

---

## Q2. Do you think Ansible is better than other configuration management tools? If yes, why?

Yes, Ansible is often better than other configuration management tools because it is agentless, easy to learn, and uses a push-based model with readable YAML syntax. It requires minimal setup and is very flexible for both configuration management and automation tasks. However, in environments that need continuous configuration enforcement, tools like Puppet or Chef may be more suitable.

---

## Q3. Can you write an Ansible playbook to install httpd service? 

```bash
- name: Install and start Apache
  hosts: all
  become: yes

  tasks:
    - name: Install apache
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Start apache service
      service:
        name: apache2
        state: started
        enabled: yes
```

---

## Q4. How does Ansible help your organization? 

Ansible helps the organization by automating configuration and operational tasks, reducing manual effort and human errors. It ensures consistency across servers, speeds up deployments and scaling, improves security compliance, and makes system management more reliable and efficient.

---

## Q5. What is Ansible dynamic inventory?

Ansible dynamic inventory is a way to automatically discover and manage hosts instead of defining them manually in a static inventory file. It uses scripts or plugins to fetch current host information from cloud platforms or other systems, making it ideal for dynamic environments like cloud and Kubernetes.

Situation

- Your organization runs applications on AWS EC2.

- Servers are created and deleted using Auto Scaling

- IP addresses change frequently

Environment has:

- dev

- staging

- prod

👉 Maintaining a static inventory file is impossible.

Problem without Dynamic Inventory

- New EC2 instances are launched

- Ansible inventory file is outdated

- Playbooks fail or miss new servers

- Manual updates cause mistakes

👉 Automation breaks.

Solution: Ansible Dynamic Inventory

You configure Ansible to use AWS dynamic inventory.

How it works

- Ansible connects to AWS

- Fetches EC2 instances automatically

- Filters instances using tags:

- env=prod

- app=web

Now inventory is always up to date.

You can say this:

In a cloud environment where EC2 instances are created and deleted dynamically, maintaining a static inventory is difficult. We use Ansible dynamic inventory to automatically discover EC2 instances based on tags. This ensures Ansible always targets the correct servers, even when Auto Scaling adds or removes instances.

---

## Q6. What is Ansible Tower

In our organization, we use Ansible Tower so different teams can run automation jobs without accessing servers directly. Tower controls access, stores credentials securely, schedules jobs, and provides logs for auditing.

---

## Q7. How do you manage RBAC (Role-Based Access Control) on Ansible Tower?

Step 1️⃣ Create Users

First:

Create users in Ansible Tower

Or sync users from LDAP / SSO

Each user represents a real person or team member.

👉 No shared logins (very important).

Step 2️⃣ Create Teams (Best Practice)

Instead of managing permissions user-by-user:

Create teams like:

Dev

Ops

QA

SRE

Add users to teams

👉 Permissions are given to teams, not individuals.
This makes management easy.

Step 3️⃣ Assign Roles (Core of RBAC)

Ansible Tower has predefined roles.

Common roles:

Admin → full control

Execute → can run jobs

Read → view only

Update → edit playbooks/configs

Use → use credentials or inventories

👉 You don’t invent roles, you assign existing ones.

Step 4️⃣ Apply Roles to Specific Resources

RBAC in Tower is resource-based.

You assign roles on:

Inventories

Job templates

Projects

Credentials

Example:

Dev team → Execute role on dev job template

Ops team → Admin role on prod inventory

QA team → Read-only access

👉 Teams only see and touch what they need.

Step 5️⃣ Control Credentials Access (Very Important 🔐)

Credentials are sensitive.

Best practice:

Only limited teams can use credentials

No one sees passwords or SSH keys

Credentials are reused safely

👉 This prevents secret leakage.

Step 6️⃣ Audit and Review Access

Ansible Tower keeps:

Job history

User activity logs

You can check:

Who ran which job

When it was run

What inventory was affected

👉 Perfect for security and compliance.

---

## Q8. What is Ansible Galaxy command and why is it used?

Ansible Galaxy is a community repository for Ansible roles, and the ansible-galaxy command is used to download, create, and manage these roles.

---

## Q9. Can you explain the structure of an Ansible playbook using roles? 

In Ansible, roles are used to organize playbooks into a structured directory format. A role contains folders like tasks, handlers, templates, files, vars, and defaults. The main playbook simply calls the roles, while all logic is kept inside the role. This makes automation reusable, readable, and easy to maintain.

---

## Q10. What are handlers in Ansible and why are they used?

Handlers in Ansible are special tasks that run only when notified by another task. They are mainly used to perform actions like restarting or reloading services after a configuration change. This helps avoid unnecessary restarts and makes automation more efficient and reliable.

---

## Q11. I would like to run a specific set of tasks only on Windows VMs and not on Linux VMs. Is it possible? 

Yes, it is possible. Ansible collects OS facts automatically, and we can use conditions like when: ansible_os_family == "Windows" to run specific tasks only on Windows VMs. Linux systems will automatically skip those tasks. This allows us to manage both Windows and Linux using a single playbook

___

## Q12. Does Ansible support parallel execution? 

Yes, Ansible supports parallel execution. By default, it runs tasks in parallel across multiple hosts using forks. The level of parallelism can be controlled using the forks setting or execution strategies, which helps speed up automation in large environments.

---

## Q13. What is the protocol that Ansible uses to connect to Windows VMs?

Ansible uses WinRM (Windows Remote Management) to connect to Windows virtual machines.

---

## Q14. How do you handle secrets in Ansible?

We store database passwords in encrypted Ansible Vault files. During playbook execution, the vault password is provided securely, and Ansible decrypts the secrets at runtime without exposing them in logs or code.

---

## Q15. Can we use Ansible for IaC (Infrastructure as Code)? If yes, can you compare it with any other IaC tools like Terraform?

Yes, Ansible can be used for Infrastructure as Code, especially for provisioning and configuring resources. However, tools like Terraform are better suited for IaC because they are declarative and maintain state, which helps manage infrastructure lifecycle effectively. In practice, Terraform is often used to provision infrastructure, and Ansible is used to configure and deploy applications on top of it.

When to choose Ansible for IaC

Use Ansible for IaC when:

Infrastructure is small

No complex lifecycle needed

You want simple automation

You already use Ansible heavily

When Terraform is the better choice

Use Terraform when:

Cloud infrastructure is large

You need rollback & state tracking

Multi-cloud setups

Production-grade IaC

---

## Q16. What do you think Ansible can improve?

Ansible can improve in areas like state management, performance at very large scale, and better error handling. It could also provide more consistent dry-run behavior and smoother Windows support. However, it remains a strong and simple automation tool, especially when combined with other tools for infrastructure provisioning.

---

## Q17. What is an Ansible Playbook?

A playbook is a set of instructions that Ansible follows to automate work.

---

## Q18. How do you define inventory in Ansible?

In Ansible, inventory is the place where you define which servers Ansible will manage.

1️⃣ Static Inventory (Most basic way)

This is a file where you manually list servers.

What it contains

IP addresses or hostnames

Groups of servers (optional)

Example idea:

web servers

database servers

prod / dev servers

👉 Good for small or fixed environments.

2️⃣ Grouping hosts (Very important)

You can group servers based on purpose.

Why?

Run tasks only on specific servers

Keep automation clean

Example concept:

web group → Apache tasks

db group → Database tasks

👉 Groups make playbooks reusable.

3️⃣ Inventory with variables

Inventory can also store:

SSH user

Port

Environment-specific values

Why useful?

Different servers, different settings

No hardcoding in playbooks

👉 Keeps configuration centralized.

4️⃣ Dynamic Inventory (Real-world cloud setups)

In cloud environments, servers change often.

Dynamic inventory:

Fetches servers automatically

From AWS, Azure, GCP, Kubernetes

No manual IP management

Example idea:

All EC2 instances with tag env=prod

👉 Perfect for auto-scaling environments.

---

## Q19. Explain the concept of idempotence in Ansible.

Idempotence in Ansible means that running the same playbook multiple times produces the same result and does not make unnecessary changes if the system is already in the desired state. This ensures automation is safe, predictable, and reliable.

---

## Q20. Explain the difference between Ansible ad-hoc commands and playbooks.

Ansible ad-hoc commands are used for quick, one-time tasks executed directly from the command line, such as checking system status or restarting a service. Playbooks, on the other hand, are YAML files that define a series of tasks and are used for repeatable, structured, and production-level automation.

---

## Q21. Explain the concept of Ansible Facts.

In Ansible, facts are system details automatically collected by Ansible from target machines.

What kind of information facts contain

Ansible facts include things like:

Operating system (Linux / Windows)

OS family (RedHat, Debian, Windows)

IP address

Hostname

CPU details

Memory size

Disk information

Network interfaces

👉 Basically, server identity + system info.


---

## Q22. Explain how you can integrate Ansible with other tools and technologies.

Ansible integrates with CI/CD tools like Jenkins, cloud platforms such as AWS and Azure, IaC tools like Terraform, container platforms like Kubernetes, and monitoring and logging systems. It acts as an automation layer that connects different tools and ensures consistent configuration and deployment across environments.


---


## Q23. You need to provision a new server instance on AWS with Ansible. How would you approach this task?

o provision a new server on AWS using Ansible, I first configure AWS credentials and permissions. Then I use Ansible AWS modules to create an EC2 instance with the required AMI, instance type, and security settings. Once the instance is running and reachable, I use dynamic inventory to discover it and apply Ansible roles to configure the server and deploy applications. In larger setups, Terraform is often used for provisioning and Ansible for configuration.


---

## Q24. You have multiple web servers running Apache, and you need to ensure that a specific configuration file is consistent across all servers. How would you achieve this with Ansible?

I would create an Ansible role that defines the desired state of the Apache configuration. The role would use a template to manage the configuration file with variables, ensuring flexibility across environments. This configuration would be distributed to all web servers in the inventory, and a handler would be used to restart the Apache service only when the configuration changes, ensuring consistency and avoiding unnecessary restarts.

---

## Q25. Your team is deploying a new version of a web application across multiple servers. How would you use Ansible to automate the deployment process?

I would automate the deployment by creating an Ansible playbook that pulls the latest version of the application from version control and deploys it across all target servers. The playbook would stop the currently running application, copy the new application files, apply any required configuration updates, and then start the updated application. By running this process through Ansible, the deployment remains consistent, repeatable, and reliable across all servers.
