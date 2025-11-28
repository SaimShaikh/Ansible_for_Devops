# Ansible_Commands

# 📌 Understanding Essential Flags

| Flag | Meaning | When to Use |
|------|---------|-------------|
| `-i` | Inventory file | When running Ad-Hoc commands or playbooks |
| `-m` | Module name | When you want Ansible to execute a specific module like `ping`, `shell`, `copy`, `apt`, etc. |
| `-a` | Arguments | Used with `-m` to pass command arguments |
| `-b` / `--become` | Run as root (sudo) | When installing packages or modifying system settings |
| `-K` | Ask sudo password | When your user requires sudo password |
| `-u` | Specify remote user | If not using default `ansible_user` |
| `--limit` | Run on specific host(s) | Useful when narrowing execution |
| `--check` | Dry-run mode | To preview changes without applying |
| `--diff` | Show file differences | Useful with file/template tasks |


### 1. Main Ansible Tools/Commands

| Command | Description | Basic Usage |
|---------|-------------|-------------|
| `ansible` | Run ad-hoc commands on hosts without playbooks | `ansible <hosts> -i <inventory> -m <module>` |
| `ansible-playbook` | Run playbooks (YAML files with multiple plays/tasks) | `ansible-playbook <playbook.yml> -i <inventory>` |
| `ansible-inventory` | Manage and view inventory, validate hosts configuration | `ansible-inventory -i <inventory> --list` |
| `ansible-doc` | View documentation for modules and plugins | `ansible-doc <module_name>` |
| `ansible-galaxy` | Download and manage roles and collections from Ansible Galaxy | `ansible-galaxy role install <role_name>` |
| `ansible-vault` | Encrypt and decrypt sensitive files (passwords, keys, secrets) | `ansible-vault create <file>` |
| `ansible-pull` | Pull configuration from Git repository (pull-based automation) | `ansible-pull -U <git_url>` |
| `ansible-console` | Interactive console for running Ansible commands interactively | `ansible-console -i <inventory>` |
| `ansible-config` | View and validate Ansible configuration settings | `ansible-config view` |
| `ansible-lint` | Lint playbooks to check for best practices and errors | `ansible-lint <playbook.yml>` |
| `ansible-runner` | Execute Ansible in a container or as a service | `ansible-runner run <project_dir>` |
| `ansible-test` | Test Ansible modules and collections | `ansible-test unit <test_file>` |

---

## 2. `ansible` Command – Ad-Hoc Commands (Most Common Options)

| Command/Option | Description |
|----------------|-------------|
| `ansible <hosts>` | Target specific hosts or groups |
| `ansible all` | Target all hosts in inventory |
| `-i <inventory>` | Specify inventory file path |
| `-m <module>` | Specify which module to use |
| `-a <arguments>` | Pass arguments to the module |
| `-u <user>` | SSH username to connect with |
| `-k` | Ask for SSH password (interactive) |
| `--private-key` | Specify SSH private key file path |
| `--become` | Use sudo/su for privilege escalation |
| `--become-user` | Which user to become (default: root) |
| `--become-method` | How to escalate (sudo, su, pbrun, etc.) |
| `-c <connection>` | Connection type (ssh, local, etc.) |
| `-l <limit>` or `--limit` | Limit execution to specific hosts/groups |
| `-v` | Verbose output (show more details) |
| `-vv` | Very verbose output |
| `-vvv` | Very very verbose (debug level) |
| `-vvvv` | Even more verbose (shows every detail) |
| `--check` | Dry-run mode (don't make actual changes) |
| `--diff` | Show differences between current and desired state |
| `-e` | Pass extra variables |
| `--extra-vars` | Pass extra variables (longer form) |
| `-f <number>` | Number of parallel processes (forks) |
| `-T <timeout>` | Task timeout in seconds |
| `--syntax-check` | Check playbook/task syntax without running |
| `--flush-cache` | Clear fact cache |
| `-t <tags>` | Run only tasks with specific tags |
| `--skip-tags` | Skip tasks with specific tags |
| `--step` | Ask before executing each task |
| `--start-at-task` | Start execution from specific task |
| `--ask-become-pass` | Ask for sudo password interactively |

---

## 3. `ansible-playbook` Command – Playbook Options

| Command/Option | Description |
|----------------|-------------|
| `ansible-playbook <playbook>` | Run a playbook |
| `-i <inventory>` | Specify inventory file |
| `-l <hosts>` | Limit to specific hosts/groups |
| `--check` | Dry-run mode (don't make changes) |
| `--diff` | Show file differences before/after |
| `-v`, `-vv`, `-vvv`, `-vvvv` | Increase verbosity level |
| `-u <user>` | SSH username |
| `-k` | Ask for SSH password |
| `--private-key` | SSH private key file path |
| `--become` | Use sudo/su |
| `--become-user` | User to become |
| `--become-method` | Escalation method |
| `--ask-become-pass` | Ask for sudo password |
| `-e` | Pass extra variables |
| `--extra-vars` | Pass extra variables (longer form) |
| `-t <tags>` | Run only specific tags |
| `--skip-tags` | Skip specific tags |
| `--start-at-task` | Start from specific task |
| `--step` | Ask before each task |
| `-f <number>` | Number of parallel processes (forks) |
| `-T <timeout>` | Task timeout in seconds |
| `--vault-password-file` | Path to vault password file |
| `--ask-vault-pass` | Ask for vault password |
| `--flush-cache` | Clear fact cache |
| `--force-handlers` | Force handlers even if tasks fail |
| `--list-hosts` | List hosts that would be affected |
| `--list-tasks` | List all tasks without running |
| `--list-tags` | List all available tags |
| `--syntax-check` | Check syntax only |
| `-M <module_path>` | Specify custom module path |
| `-e` | Set environment variables |

---

## 4. `ansible-inventory` Command – Inventory Options

| Command/Option | Description |
|----------------|-------------|
| `ansible-inventory -i <inventory>` | Specify inventory file |
| `--list` | Show all hosts in JSON format |
| `--graph` | Show inventory as a tree/graph structure |
| `--host <hostname>` | Show variables for specific host |
| `--vars` | Show all variables for each host |
| `--toml` | Output in TOML format |
| `--yaml` | Output in YAML format |
| `--json` | Output in JSON format (default) |
| `-v` | Verbose output |
| `--group <group>` | Show specific group |
| `--export` | Export inventory to file |

---

## 5. `ansible-doc` Command – Module Documentation

| Command/Option | Description |
|----------------|-------------|
| `ansible-doc <module>` | Show documentation for specific module |
| `ansible-doc -l` | List all available modules |
| `ansible-doc -l \| grep <keyword>` | Search modules by keyword |
| `ansible-doc -s <module>` | Show module snippet/example |
| `ansible-doc -p <plugin>` | Show plugin documentation |
| `ansible-doc -t <type>` | Show documentation for specific plugin type |
| `ansible-doc --version` | Show Ansible version |
| `-v` | Verbose output |

---

## 6. `ansible-vault` Command – Encrypt/Decrypt Files

| Command/Option | Description |
|----------------|-------------|
| `ansible-vault create <file>` | Create new encrypted file |
| `ansible-vault encrypt <file>` | Encrypt existing file |
| `ansible-vault decrypt <file>` | Decrypt file permanently |
| `ansible-vault view <file>` | View encrypted file (read-only) |
| `ansible-vault edit <file>` | Edit encrypted file |
| `ansible-vault rekey <file>` | Change password for encrypted file |
| `--vault-password-file` | Path to vault password file |
| `--ask-vault-pass` | Ask for vault password interactively |
| `-v` | Verbose output |

---

## 7. `ansible-galaxy` Command – Roles and Collections

| Command/Option | Description |
|----------------|-------------|
| `ansible-galaxy role install <role>` | Install a role from Galaxy |
| `ansible-galaxy role list` | List installed roles |
| `ansible-galaxy role remove <role>` | Remove installed role |
| `ansible-galaxy role search <keyword>` | Search for roles on Galaxy |
| `ansible-galaxy role info <role>` | Show role information |
| `ansible-galaxy role init <role_name>` | Create new role structure |
| `ansible-galaxy collection install <collection>` | Install a collection |
| `ansible-galaxy collection list` | List installed collections |
| `ansible-galaxy collection search <keyword>` | Search collections on Galaxy |
| `-r <requirements_file>` | Install from requirements file |
| `-p <path>` | Install to specific path |
| `-f` or `--force` | Force reinstall/overwrite |
| `-v` | Verbose output |
| `--api-key` | Ansible Galaxy API key |

---

## 8. `ansible-pull` Command – Pull-Based Automation

| Command/Option | Description |
|----------------|-------------|
| `ansible-pull -U <url>` | Git repository URL to pull from |
| `-d <directory>` | Directory to clone into |
| `-C <checkout>` | Git branch/tag/commit to checkout |
| `--clean` | Clean working directory before pulling |
| `--accept-host-key` | Accept host SSH key automatically |
| `-i <inventory>` | Inventory file to use |
| `-m <module>` | Module to use for running playbook |
| `-M <module_path>` | Module path |
| `--purge` | Purge repository directory before clone |
| `--full` | Do a full clone (not shallow) |
| `-e` | Pass extra variables |
| `-v` | Verbose output |

---

## 9. `ansible-console` Command – Interactive Console

| Command/Option | Description |
|----------------|-------------|
| `ansible-console -i <inventory>` | Open interactive console for specific inventory |
| `<hosts> -m <module> -a <arguments>` | Run command in console |
| `exit` or `quit` | Exit the console |
| `-u <user>` | SSH username |
| `--private-key` | SSH private key |
| `--become` | Use sudo |
| `-v` | Verbose output |
| `cd <group>` | Change target group |

---

## 10. `ansible-config` Command – Configuration Management

| Command/Option | Description |
|----------------|-------------|
| `ansible-config view` | Show current Ansible configuration |
| `ansible-config dump` | Show all configuration (defaults + custom) |
| `ansible-config dump --only-changed` | Show only non-default settings |
| `ansible-config list` | List all configuration options |
| `--format <format>` | Output format (yaml, json, etc.) |
| `-c <config>` | Specify config file |
| `-v` | Verbose output |

---

## 11. Common Ansible Modules (Used with `ansible` and `ansible-playbook`)

| Module | Purpose |
|--------|---------|
| `command` | Execute raw commands on remote system |
| `shell` | Execute shell commands with pipes, redirects |
| `apt` | Debian/Ubuntu package manager |
| `yum` | RedHat/CentOS/Fedora package manager |
| `dnf` | DNF package manager |
| `pacman` | Arch Linux package manager |
| `apk` | Alpine Linux package manager |
| `package` | Generic package manager (auto-detect) |
| `service` | Manage system services (old method) |
| `systemd` | Manage systemd services (new method) |
| `copy` | Copy files from control node to target |
| `template` | Copy files with template variable substitution |
| `file` | Manage files, directories, permissions |
| `directory` | Manage directories |
| `lineinfile` | Edit lines in text files |
| `replace` | Replace text in files |
| `user` | Manage user accounts |
| `group` | Manage user groups |
| `authorized_key` | Add SSH public keys to authorized_keys |
| `git` | Clone or pull git repositories |
| `cron` | Manage cron jobs |
| `mount` | Manage filesystem mounts |
| `debug` | Print debug messages and variables |
| `fail` | Fail and stop playbook |
| `assert` | Assert conditions are true |
| `wait_for` | Wait for condition to be true |
| `pause` | Pause playbook execution |
| `block` | Create a block of tasks |
| `set_fact` | Set variables for current playbook run |
| `register` | Register output as a variable |
| `vars` | Define variables |
| `include_tasks` | Include external task file |
| `import_tasks` | Import external task file |
| `include_vars` | Include external variables file |
| `include_role` | Include role tasks |
| `import_role` | Import role tasks |
| `meta` | Control playbook behavior |
| `handlers` | Define handlers for events |
| `notify` | Trigger handlers |
| `when` | Conditional execution of tasks |
| `loop` | Loop over items |
| `with_items` | Loop variant (older) |
| `with_dict` | Loop over dictionary |
| `with_nested` | Loop with nested items |
| `until` | Retry task until condition met |
| `retries` | Number of retry attempts |
| `delay` | Delay between retries |
| `changed_when` | Mark task as changed based on condition |
| `failed_when` | Mark task as failed based on condition |
| `ignore_errors` | Ignore errors in task |
| `docker_container` | Manage Docker containers |
| `docker_image` | Manage Docker images |
| `kubernetes.core.k8s` | Manage Kubernetes resources |
| `aws_ec2` | AWS EC2 cloud module |
| `gcp_compute` | Google Cloud Platform compute module |
| `azure_rm_virtualmachine` | Azure virtual machine module |
| `uri` | Make HTTP requests |
| `get_url` | Download files from HTTP/HTTPS |
| `unarchive` | Extract archive files |
| `archive` | Create archive files |
| `stat` | Get file/directory stats |
| `find` | Find files matching criteria |
| `shell` | Execute shell commands |
| `raw` | Run commands without Python dependency |
| `script` | Run script on remote system |
| `firewalld` | Manage firewalld rules |
| `iptables` | Manage iptables rules |
| `selinux` | Manage SELinux |
| `sysctl` | Manage sysctl settings |
| `environment` | Set environment variables |
| `hostname` | Set/change hostname |
| `timezone` | Set/change timezone |
| `locale_gen` | Generate locales |
| `apt_key` | Add/remove apt keys |
| `apt_repository` | Add/remove apt repositories |
| `yum_repository` | Add/remove yum repositories |
| `zypper` | openSUSE package manager |
| `bundler` | Manage Ruby bundler |
| `npm` | Manage Node.js npm packages |
| `pip` | Manage Python pip packages |
| `gem` | Manage Ruby gems |
| `composer` | Manage PHP composer |
| `make` | Run make commands |
| `cmake` | Run cmake commands |
| `postgresql_db` | Manage PostgreSQL databases |
| `postgresql_user` | Manage PostgreSQL users |
| `mysql_db` | Manage MySQL databases |
| `mysql_user` | Manage MySQL users |
| `mongodb_user` | Manage MongoDB users |
| `redis` | Manage Redis |
| `rabbitmq_user` | Manage RabbitMQ users |
| `rabbitmq_vhost` | Manage RabbitMQ vhosts |
| `nginx` | Manage Nginx |
| `apache2_module` | Manage Apache modules |
| `ssl_certificate` | Manage SSL certificates |
| `openssl_certificate` | Generate OpenSSL certificates |
| `openssh_keypair` | Generate SSH keypairs |
| `openssh_public_key` | Manage SSH public keys |
| `known_hosts` | Manage SSH known_hosts file |
| `expect` | Expect module for interactive commands |
| `syslog` | Send message to syslog |
| `mail` | Send email messages |
| `slack` | Send Slack notifications |
| `sns` | Send AWS SNS notifications |
| `twilio` | Send Twilio messages |
| `grafana_dashboard` | Manage Grafana dashboards |
| `grafana_datasource` | Manage Grafana datasources |
| `influxdb_database` | Manage InfluxDB databases |
| `influxdb_user` | Manage InfluxDB users |
| `monitoring` | Generic monitoring modules |
| `nagios` | Manage Nagios monitoring |
| `zabbix_host` | Manage Zabbix hosts |
| `zabbix_user` | Manage Zabbix users |

---

## 12. Common Ansible Ad-Hoc Command Examples

| Command | What It Does |
|---------|------------|
| `ansible all -i inventory -m ping` | Test connectivity to all hosts |
| `ansible web -i inventory -m ping` | Test connectivity to "web" group |
| `ansible web1 -i inventory -m setup` | Get system facts/info about web1 |
| `ansible web -i inventory -m shell -a "uptime"` | Run uptime command on web servers |
| `ansible web -i inventory -m command -a "ls -la"` | List directory on web servers |
| `ansible web -i inventory -m copy -a "src=/tmp/file dest=/etc/file"` | Copy file to servers |
| `ansible web -i inventory -m apt -a "name=nginx state=present"` | Install nginx package |
| `ansible web -i inventory -m apt -a "name=nginx state=absent"` | Remove nginx package |
| `ansible web -i inventory -m apt -a "name=* state=latest"` | Update all packages |
| `ansible web -i inventory -m service -a "name=nginx state=started"` | Start nginx service |
| `ansible web -i inventory -m service -a "name=nginx state=stopped"` | Stop nginx service |
| `ansible web -i inventory -m service -a "name=nginx state=restarted"` | Restart nginx service |
| `ansible web -i inventory -m service -a "name=nginx state=reloaded"` | Reload nginx service |
| `ansible web -i inventory -m file -a "path=/tmp/file state=directory"` | Create directory |
| `ansible web -i inventory -m file -a "path=/tmp/file state=absent"` | Delete file/directory |
| `ansible web -i inventory -m file -a "path=/tmp/file mode=0755"` | Change file permissions |
| `ansible web -i inventory -m user -a "name=testuser state=present"` | Create user |
| `ansible web -i inventory -m user -a "name=testuser state=absent"` | Delete user |
| `ansible web -i inventory -m authorized_key -a "user=ubuntu key='{{ lookup(\"file\", \"/tmp/key.pub\") }}'"` | Add SSH public key |
| `ansible web -i inventory -m lineinfile -a "path=/etc/hosts line='10.0.0.1 host1'"` | Add line to file |
| `ansible web -i inventory -m git -a "repo=https://github.com/user/repo dest=/tmp/repo"` | Clone git repository |
| `ansible web -i inventory -m cron -a "name='backup' hour='0' job='/usr/bin/backup.sh'"` | Add cron job |
| `ansible web -i inventory -m debug -a "msg='Hello {{ inventory_hostname }}'"` | Print debug message |
| `ansible web -i inventory -m uri -a "url=http://example.com method=GET"` | Make HTTP request |
| `ansible web -i inventory -m get_url -a "url=http://example.com/file dest=/tmp/"` | Download file |
| `ansible web -i inventory -m unarchive -a "src=/tmp/archive.tar.gz dest=/opt/"` | Extract archive |
| `ansible web -i inventory -m stat -a "path=/tmp/file"` | Get file stats |
| `ansible web -i inventory -m find -a "paths=/tmp patterns='*.log'"` | Find files |
| `ansible web -i inventory -m replace -a "path=/etc/hosts regexp='old' replace='new'"` | Replace text in file |
| `ansible web -i inventory --become -m command -a "systemctl status nginx"` | Run as sudo |

---

## 13. Common Playbook Options Quick Reference

| Option | Example | Purpose |
|--------|---------|---------|
| `-i` | `ansible-playbook site.yml -i inventory` | Specify inventory |
| `-l` | `ansible-playbook site.yml -l web` | Limit to hosts/groups |
| `--check` | `ansible-playbook site.yml --check` | Dry-run mode |
| `--diff` | `ansible-playbook site.yml --diff` | Show changes |
| `-v` | `ansible-playbook site.yml -v` | Verbose (1x) |
| `-vv` | `ansible-playbook site.yml -vv` | Verbose (2x) |
| `-vvv` | `ansible-playbook site.yml -vvv` | Verbose (3x) |
| `-u` | `ansible-playbook site.yml -u ubuntu` | SSH user |
| `-k` | `ansible-playbook site.yml -k` | Ask SSH password |
| `--private-key` | `ansible-playbook site.yml --private-key ~/.ssh/key` | SSH key file |
| `--become` | `ansible-playbook site.yml --become` | Use sudo |
| `--ask-become-pass` | `ansible-playbook site.yml --ask-become-pass` | Ask sudo password |
| `-e` | `ansible-playbook site.yml -e "var=value"` | Extra variables |
| `-t` | `ansible-playbook site.yml -t install` | Run only tags |
| `--skip-tags` | `ansible-playbook site.yml --skip-tags restart` | Skip tags |
| `--start-at-task` | `ansible-playbook site.yml --start-at-task "Deploy"` | Start from task |
| `--step` | `ansible-playbook site.yml --step` | Ask before each task |
| `-f` | `ansible-playbook site.yml -f 10` | Parallel processes |
| `-T` | `ansible-playbook site.yml -T 30` | Task timeout (seconds) |
| `--syntax-check` | `ansible-playbook site.yml --syntax-check` | Check syntax only |
| `--list-hosts` | `ansible-playbook site.yml --list-hosts` | List affected hosts |
| `--list-tasks` | `ansible-playbook site.yml --list-tasks` | List tasks only |
| `--list-tags` | `ansible-playbook site.yml --list-tags` | List available tags |
| `--vault-password-file` | `ansible-playbook site.yml --vault-password-file ~/pw` | Vault password file |
| `--ask-vault-pass` | `ansible-playbook site.yml --ask-vault-pass` | Ask vault password |
| `--flush-cache` | `ansible-playbook site.yml --flush-cache` | Clear fact cache |
| `--force-handlers` | `ansible-playbook site.yml --force-handlers` | Run handlers on failure |

---

## 14. Useful Environment Variables

| Variable | Purpose |
|----------|---------|
| `ANSIBLE_HOSTS` | Inventory file path |
| `ANSIBLE_INVENTORY` | Inventory file path |
| `ANSIBLE_CONFIG` | Config file path |
| `ANSIBLE_VAULT_PASSWORD_FILE` | Vault password file path |
| `ANSIBLE_REMOTE_USER` | Remote SSH user |
| `ANSIBLE_PRIVATE_KEY_FILE` | Private key file path |
| `ANSIBLE_TIMEOUT` | SSH timeout in seconds |
| `ANSIBLE_FORKS` | Number of parallel processes |
| `ANSIBLE_LIBRARY` | Module search path |
| `ANSIBLE_FILTER_PLUGINS` | Filter plugin path |
| `ANSIBLE_CALLBACK_PLUGINS` | Callback plugin path |
| `ANSIBLE_BECOME_METHOD` | Privilege escalation method |
| `ANSIBLE_BECOME_USER` | User to become |
| `ANSIBLE_VERBOSITY` | Verbosity level (0-4) |
| `ANSIBLE_FORCE_COLOR` | Force colored output |
| `ANSIBLE_NOCOLOR` | Disable colored output |
| `ANSIBLE_NOCOWS` | Disable cowsay |

---

## 15. Quick Command Reference by Task

### Testing & Validation

| Task | Command |
|------|---------|
| Test connectivity | `ansible all -i inventory -m ping` |
| Check syntax | `ansible-playbook site.yml --syntax-check` |
| List hosts | `ansible-inventory -i inventory --list` |
| View host tree | `ansible-inventory -i inventory --graph` |
| Get host variables | `ansible-inventory -i inventory --host web1` |

### Running Playbooks

| Task | Command |
|------|---------|
| Run playbook | `ansible-playbook site.yml -i inventory` |
| Dry-run | `ansible-playbook site.yml -i inventory --check` |
| Dry-run with diff | `ansible-playbook site.yml -i inventory --check --diff` |
| Run on one group | `ansible-playbook site.yml -i inventory -l web` |
| Run specific tags | `ansible-playbook site.yml -i inventory -t install` |
| Skip tags | `ansible-playbook site.yml -i inventory --skip-tags restart` |

### Ad-Hoc Commands

| Task | Command |
|------|---------|
| Run command | `ansible web -i inventory -m shell -a "uptime"` |
| Copy file | `ansible web -i inventory -m copy -a "src=/tmp/f dest=/etc/f"` |
| Install package | `ansible web -i inventory -m apt -a "name=nginx"` |
| Restart service | `ansible web -i inventory -m service -a "name=nginx state=restarted"` |
| Get facts | `ansible web1 -i inventory -m setup` |

### File Management

| Task | Command |
|------|---------|
| Create directory | `ansible web -i inventory -m file -a "path=/tmp/dir state=directory"` |
| Change permissions | `ansible web -i inventory -m file -a "path=/tmp/f mode=0755"` |
| Delete file | `ansible web -i inventory -m file -a "path=/tmp/f state=absent"` |
| Edit file line | `ansible web -i inventory -m lineinfile -a "path=/etc/f line='text'"` |
| Replace text | `ansible web -i inventory -m replace -a "path=/f regexp='old' replace='new'"` |

### User Management

| Task | Command |
|------|---------|
| Create user | `ansible web -i inventory -m user -a "name=john state=present"` |
| Delete user | `ansible web -i inventory -m user -a "name=john state=absent"` |
| Add SSH key | `ansible web -i inventory -m authorized_key -a "user=ubuntu key='..'"` |

### Service Management

| Task | Command |
|------|---------|
| Start service | `ansible web -i inventory -m service -a "name=nginx state=started"` |
| Stop service | `ansible web -i inventory -m service -a "name=nginx state=stopped"` |
| Restart service | `ansible web -i inventory -m service -a "name=nginx state=restarted"` |
| Enable at boot | `ansible web -i inventory -m service -a "name=nginx enabled=yes"` |
| Disable at boot | `ansible web -i inventory -m service -a "name=nginx enabled=no"` |

### Package Management

| Task | Command |
|------|---------|
| Install (Ubuntu) | `ansible web -i inventory -m apt -a "name=nginx state=present"` |
| Install (RedHat) | `ansible web -i inventory -m yum -a "name=nginx state=present"` |
| Update cache | `ansible web -i inventory -m apt -a "update_cache=yes"` |
| Upgrade all | `ansible web -i inventory -m apt -a "name=* state=latest"` |
| Remove package | `ansible web -i inventory -m apt -a "name=nginx state=absent"` |

### Encryption/Security

| Task | Command |
|------|---------|
| Create vault | `ansible-vault create secrets.yml` |
| Edit vault | `ansible-vault edit secrets.yml` |
| View vault | `ansible-vault view secrets.yml` |
| Encrypt file | `ansible-vault encrypt file.yml` |
| Decrypt file | `ansible-vault decrypt file.yml` |
| Run with vault | `ansible-playbook site.yml --ask-vault-pass` |

### Roles & Collections

| Task | Command |
|------|---------|
| Install role | `ansible-galaxy role install user.role` |
| List roles | `ansible-galaxy role list` |
| Search roles | `ansible-galaxy role search nginx` |
| Install collection | `ansible-galaxy collection install community.general` |
| List collections | `ansible-galaxy collection list` |
| Create role | `ansible-galaxy role init my_role` |

### Documentation

| Task | Command |
|------|---------|
| Module docs | `ansible-doc apt` |
| List modules | `ansible-doc -l` |
| Search modules | `ansible-doc -l \| grep copy` |
| Module snippet | `ansible-doc -s apt` |

### Configuration

| Task | Command |
|------|---------|
| View config | `ansible-config view` |
| Dump config | `ansible-config dump` |
| Show only changes | `ansible-config dump --only-changed` |
| List options | `ansible-config list` |

---

## 16. Common Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success (all tasks completed without error) |
| 1 | Error (playbook failed, host unreachable, etc.) |
| 2 | User interrupted (Ctrl+C) |
| 3+ | Reserved for custom scripts |

---

**This is the complete reference table for all Ansible commands!** 🚀
