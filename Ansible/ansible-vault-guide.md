# Ansible Vault - Secure Your Secrets

A beginner-friendly guide to understand Ansible Vault, why you need it, and how to use it safely.

---

## Table of Contents
1. [Problem Scenario](#problem-scenario)
2. [What is Ansible Vault?](#what-is-ansible-vault)
3. [Why Do You Need Ansible Vault?](#why-do-you-need-ansible-vault)
4. [Benefits of Using Ansible Vault](#benefits-of-using-ansible-vault)
5. [How to Use Ansible Vault](#how-to-use-ansible-vault)
6. [Practical Examples](#practical-examples)
7. [Best Practices](#best-practices)

---

## Problem Scenario

Let's imagine you're working as a DevOps engineer, and you need to deploy a web application with a database.

**The Problem (Without Vault):**

You create an Ansible playbook like this:

```yaml
---
- name: Deploy Web Application
  hosts: all
  tasks:
  - name: Configure MySQL
    mysql_user:
      name: admin
      password: MySecretPassword123!      # ❌ Password in plain text!
      host: localhost
      state: present

  - name: Configure API credentials
    shell: |
      export API_KEY=sk-abc123xyz789      # ❌ API key visible!
      export DB_PASSWORD=SecurePass456    # ❌ Database password visible!
```

**What's wrong?**

- Anyone with access to your GitHub repository can see all your passwords and API keys
- If someone steals your repository, they have all your secrets
- Your database, API accounts, and other systems are now compromised
- You can't safely share your code with teammates

**How will Vault solve this?**

Ansible Vault **encrypts** your sensitive data (passwords, API keys, SSH keys, etc.) so they are hidden and protected. Now you can safely commit your code to GitHub without exposing secrets.

---

## What is Ansible Vault?

**Ansible Vault** is a built-in Ansible feature that **encrypts sensitive data** using a password.[55][58]

### Simple Definition

Vault is like a locked safe:
- **Without Vault**: Your secrets are on a table (everyone can see)
- **With Vault**: Your secrets are in a locked safe (only you with the key can see)

### How It Works[55][64]

Ansible Vault uses **AES256 encryption** (military-grade security) with a **symmetric key** (one password unlocks everything).[64]

**The Process:**

1. You provide a password
2. Vault encrypts your sensitive data with that password
3. The encrypted data looks like random characters (unreadable)
4. When you run Ansible, you provide the password again
5. Vault decrypts the data only when needed
6. Your playbooks run with the decrypted values

### What Can Vault Encrypt?[64]

You can encrypt:
- **Variable files** (group_vars, host_vars)
- **Entire YAML files**
- **Specific variables** (just one secret, not the whole file)
- **Configuration files**
- **Any text-based file**

---

## Why Do You Need Ansible Vault?

### Real-World Scenario

You want to deploy 100 servers with specific passwords, API keys, and SSH credentials. You also want to:
- Keep your code in GitHub
- Let your team see the playbooks
- But keep the secrets hidden
- Update passwords without changing the playbook

**Without Vault:** Impossible (secrets are always visible)

**With Vault:** Perfectly secure (secrets are encrypted)

### Common Secrets You Need to Protect[55][58][61]

- Database passwords
- API keys and tokens
- SSH private keys
- SSL certificates
- Third-party service credentials
- Admin passwords
- AWS access keys
- Any password or sensitive configuration

---

## Benefits of Using Ansible Vault

### 1. Security[55]

Your sensitive data is encrypted, not visible in plain text.

```
❌ Without Vault:
password: MySecretPassword123

✅ With Vault:
password: !vault |
  $ANSIBLE_VAULT;1.1;AES256
  66386266316462386562643932666536626230653939653061626564373738613336613432
  6434616263643237310a653261333165396231326531343336320a616261313065
```

### 2. Version Control Safety[55][56]

You can safely commit encrypted files to GitHub without exposing secrets.

```bash
# Safe to commit to GitHub
git commit -m "Add encrypted vault files"
git push origin main
```

**Important**: Only commit the encrypted vault files, not the password file!

### 3. Team Collaboration[56]

Multiple team members can use the same encrypted playbooks:
- Share the playbook and encrypted vault file
- Only share the vault password through a secure channel (password manager, not email)
- Everyone can run the same playbooks

### 4. Easy Secret Rotation[56]

Need to change a password? Update it once in the vault:
- Change password in vault
- All playbooks automatically use the new password
- No need to update multiple files

### 5. Compliance and Auditing[55][56]

Many organizations require:
- Secrets to be encrypted
- No plaintext credentials in repositories
- Audit trails of who accessed secrets

Vault helps you meet these requirements.

### 6. Simple Implementation[60]

- Built into Ansible (no additional tools needed)
- Works with existing playbooks
- Minimal setup required

---

## How to Use Ansible Vault

### Step 1: Create an Encrypted Vault File

Create a new encrypted file where you'll store secrets:

```bash
ansible-vault create secrets.yml
```

**What happens:**
- Ansible asks you to set a password
- Opens a text editor (vi, nano, etc.)
- You type your secrets in YAML format

**Example (in the editor):**

```yaml
db_password: my_secret_database_password
db_user: admin
api_key: sk-abc123xyz789
mysql_root_password: SecurePass456
```

**Save and close** the editor. Your data is now encrypted!

### Step 2: Verify the File is Encrypted

Check if the file is encrypted:

```bash
cat secrets.yml
```

**You should see:**

```
$ANSIBLE_VAULT;1.1;AES256
66386266316462386562643932666536626230653939653061626564373738613336613432
6434616263643237310a653261333165396231326531343336320a616261313065
```

This means it's successfully encrypted! The random characters are your encrypted secrets.

### Step 3: View Encrypted Data (Without Editing)

View the decrypted content without editing:

```bash
ansible-vault view secrets.yml
```

**Ansible will ask for the password, then show you:**

```
db_password: my_secret_database_password
db_user: admin
api_key: sk-abc123xyz789
mysql_root_password: SecurePass456
```

### Step 4: Edit an Encrypted File

Need to change a secret?

```bash
ansible-vault edit secrets.yml
```

**What happens:**
- Ansible decrypts the file
- Opens the editor
- You make changes
- When you save, it re-encrypts automatically
- Original file remains encrypted

### Step 5: Use Vault in Your Playbook

Reference the encrypted vault file in your playbook:

```yaml
---
- name: Deploy MySQL Database
  hosts: db_servers
  vars_files:
    - secrets.yml          # Include the encrypted vault file
  
  tasks:
  - name: Create MySQL user
    mysql_user:
      name: "{{ db_user }}"           # Using variable from vault
      password: "{{ db_password }}"   # Using variable from vault
      host: localhost
      state: present

  - name: Create database
    mysql_db:
      name: my_application
      state: present
```

### Step 6: Run Playbook with Vault Password

#### Option 1: Enter Password Interactively

```bash
ansible-playbook playbook.yml --ask-vault-pass
```

**Output:**
```
Vault password:
(Type your password)

PLAY [Deploy MySQL Database] ...
```

#### Option 2: Use a Password File

Create a password file:

```bash
# Create a file with just the password (no newline)
echo -n "your_vault_password_here" > ~/.vault_pass
```

**Important:** Protect this file!

```bash
# Make it readable only by you
chmod 600 ~/.vault_pass
```

Run playbook with the password file:

```bash
ansible-playbook playbook.yml --vault-password-file ~/.vault_pass
```

#### Option 3: Use Environment Variable (Best for CI/CD)

Set environment variable:

```bash
export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass
```

Now you don't need to specify the password file:

```bash
ansible-playbook playbook.yml
```

#### Option 4: Use ansible.cfg File

Create or edit `ansible.cfg` in your project:

```ini
[defaults]
vault_password_file = ./.vault_pass
```

Then simply run:

```bash
ansible-playbook playbook.yml
```

---

## Practical Examples

### Example 1: Encrypt an Existing File

You already have a file with secrets:

```bash
# File already exists with plain text secrets
cat vars.yml
```

Output:
```yaml
db_user: admin
db_password: plaintext_password
```

**Encrypt it:**

```bash
ansible-vault encrypt vars.yml
```

**Now:**
```bash
cat vars.yml
# Shows encrypted content
```

### Example 2: Database Configuration

**Scenario:** You need to set up MySQL with secure credentials

**Step 1: Create vault file**

```bash
ansible-vault create group_vars/db_servers/vault.yml
```

**Content (in editor):**

```yaml
vault_db_root_password: RootSecurePass123!
vault_db_user: webappuser
vault_db_user_password: WebAppPass456!
vault_db_backup_password: BackupPass789!
```

**Step 2: Create regular variables file**

```bash
cat > group_vars/db_servers/vars.yml << 'EOF'
db_server: mysql
db_version: "8.0"
db_port: 3306
EOF
```

**Step 3: Create playbook**

```yaml
---
- name: Configure Database Servers
  hosts: db_servers
  vars_files:
    - group_vars/db_servers/vars.yml
    - group_vars/db_servers/vault.yml
  
  tasks:
  - name: Set root password
    mysql_user:
      name: root
      password: "{{ vault_db_root_password }}"
      state: present

  - name: Create application user
    mysql_user:
      name: "{{ vault_db_user }}"
      password: "{{ vault_db_user_password }}"
      priv: "myapp.*:ALL"
      state: present
```

**Step 4: Run playbook**

```bash
ansible-playbook site.yml --ask-vault-pass
```

### Example 3: API Keys and Credentials

**Scenario:** Deploy an application that needs API credentials

**Vault file:**

```bash
ansible-vault create group_vars/webservers/vault.yml
```

**Content:**

```yaml
vault_github_token: ghp_abc123xyz789
vault_slack_webhook: https://hooks.slack.com/services/YOUR/WEBHOOK/URL
vault_aws_access_key: AKIAIOSFODNN7EXAMPLE
vault_aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
vault_api_database_url: postgresql://user:password@db.example.com:5432/myapp
```

**Playbook:**

```yaml
---
- name: Deploy Web Application
  hosts: webservers
  vars_files:
    - group_vars/webservers/vault.yml
  
  tasks:
  - name: Create .env file
    template:
      src: env.j2
      dest: /opt/myapp/.env
      mode: '0600'
      owner: appuser
      group: appuser

  - name: Deploy application
    shell: |
      cd /opt/myapp
      docker-compose up -d
```

**Template file (env.j2):**

```
GITHUB_TOKEN={{ vault_github_token }}
SLACK_WEBHOOK={{ vault_slack_webhook }}
AWS_ACCESS_KEY_ID={{ vault_aws_access_key }}
AWS_SECRET_ACCESS_KEY={{ vault_aws_secret_key }}
DATABASE_URL={{ vault_api_database_url }}
```

### Example 4: Encrypt Specific Strings (Not Whole File)

Sometimes you want to mix encrypted and plain text in one file:

```bash
ansible-vault encrypt_string
```

**Output:**
```
Reading plaintext input from stdin. (ctrl-d to end)
my_secret_password
!vault |
  $ANSIBLE_VAULT;1.1;AES256
  66386266316462386562643932666536626230653939653061626564373738613336613432
  6434616263643237310a653261333165396231326531343336320a616261313065
Encryption successful
```

**Then use in playbook:**

```yaml
---
- name: Example
  hosts: all
  vars:
    db_password: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      66386266316462386562643932666536626230653939653061626564373738613336613432
      6434616263643237310a653261333165396231326531343336320a616261313065
    db_user: admin  # This is plain text, mixed with encrypted
  
  tasks:
  - name: Connect to database
    mysql_user:
      name: "{{ db_user }}"
      password: "{{ db_password }}"
      state: present
```

---

## Best Practices

### 1. Never Commit Password Files

```bash
# Create .gitignore
cat >> .gitignore << 'EOF'
.vault_pass
.vault_password
vault_password.txt
*_password_file
.env
EOF

git add .gitignore
git commit -m "Add vault password files to gitignore"
```

**What to commit:**
- ✅ Encrypted vault files (secrets.yml)
- ✅ Playbooks (.yml files)
- ✅ ansible.cfg (without password file path)

**What NOT to commit:**
- ❌ Vault password file
- ❌ .env files with secrets
- ❌ Private SSH keys
- ❌ API tokens

### 2. Use Strong Passwords

Your vault password protects everything:
- Use at least 16 characters
- Mix uppercase, lowercase, numbers, and symbols
- Don't use dictionary words
- Example: `P@ssw0rd!mYV@ult#2024`

### 3. Rotate Passwords Regularly

Change your vault password periodically:

```bash
ansible-vault rekey secrets.yml
```

**What happens:**
- Asks for current password
- Asks for new password
- File is re-encrypted with new password

### 4. Secure Password File Access

If using a password file:

```bash
# Only owner can read
chmod 600 ~/.vault_pass

# Verify permissions
ls -la ~/.vault_pass
# Should show: -rw------- (600 permissions)
```

### 5. Store Password Securely

**For development:**
- Keep password in a password manager (1Password, LastPass, Bitwarden)
- Don't write it down or keep it in plain text

**For CI/CD pipelines:**
- Use CI/CD system's secret management (GitHub Secrets, GitLab CI Variables)
- Inject password at runtime
- Never hardcode in configuration

### 6. Organize Secrets by Environment

```
ansible/
├── group_vars/
│   ├── production/
│   │   ├── vars.yml
│   │   └── vault.yml         # Encrypted
│   ├── staging/
│   │   ├── vars.yml
│   │   └── vault.yml         # Encrypted
│   └── development/
│       ├── vars.yml
│       └── vault.yml         # Encrypted
├── playbooks/
│   ├── site.yml
│   └── deploy.yml
└── .gitignore
```

### 7. Use Meaningful Variable Names

Make it clear which variables are encrypted:

```yaml
# ✅ Good (clear naming)
vault_db_password: secret_value
vault_api_key: secret_value
vault_ssh_private_key: secret_value

# ❌ Not clear
password: secret_value
api: secret_value
key: secret_value
```

### 8. Document What's in Vault

Create a README documenting vault variables:

```markdown
## Vault Variables Documentation

### Database Credentials
- `vault_db_user`: Database username
- `vault_db_password`: Database password
- `vault_db_host`: Database host

### API Keys
- `vault_github_token`: GitHub API token
- `vault_slack_webhook`: Slack webhook URL

### SSH Keys
- `vault_ssh_private_key`: SSH private key for deployments
```

### 9. Limit Vault Access

In a team:
- Only share password with authorized people
- Use environment variables for CI/CD
- Audit who accesses secrets
- Consider tools like HashiCorp Vault for enterprise

### 10. No Logs of Secrets

Prevent Ansible from logging sensitive values:

```yaml
---
- name: Configure Database
  hosts: all
  tasks:
  - name: Create user (no log)
    mysql_user:
      name: admin
      password: "{{ vault_db_password }}"
      state: present
    no_log: true    # ← Hides this task from output
```

---

## Vault Commands Reference

### Basic Commands

```bash
# Create new encrypted file
ansible-vault create secrets.yml

# Encrypt existing file
ansible-vault encrypt vars.yml

# Decrypt file (permanently)
ansible-vault decrypt secrets.yml

# View encrypted file (doesn't edit)
ansible-vault view secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Change vault password
ansible-vault rekey secrets.yml

# Encrypt a string
ansible-vault encrypt_string
```

### Running Playbooks with Vault

```bash
# Ask for password interactively
ansible-playbook playbook.yml --ask-vault-pass

# Use password file
ansible-playbook playbook.yml --vault-password-file ~/.vault_pass

# Use environment variable
export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass
ansible-playbook playbook.yml
```

---

## Common Issues and Solutions

### Issue 1: "Vault password is incorrect"

**Problem:** Wrong password provided

**Solution:**
```bash
# Try again with correct password
ansible-playbook playbook.yml --ask-vault-pass
```

### Issue 2: "Vault file not found"

**Problem:** Vault file path is wrong

**Solution:**
```bash
# Check file exists
ls -la secrets.yml

# Use correct path in vars_files
vars_files:
  - ./secrets.yml    # Correct path
```

### Issue 3: "Permission denied .vault_pass"

**Problem:** Password file has wrong permissions

**Solution:**
```bash
# Fix permissions
chmod 600 ~/.vault_pass
```

### Issue 4: "Decrypted content is visible in logs"

**Problem:** Ansible is printing secrets in output

**Solution:**
```yaml
tasks:
- name: Task with secrets
  some_module:
    password: "{{ vault_password }}"
  no_log: true    # ← Hides sensitive output
```

---

## Summary

**Ansible Vault is essential for:**
- Keeping secrets safe in version control
- Team collaboration with encrypted credentials
- Meeting security and compliance requirements
- Protecting APIs, passwords, and private keys

**Quick Start:**

1. Create encrypted vault: `ansible-vault create secrets.yml`
2. Add your secrets in YAML format
3. Reference in playbook: `vars_files: - secrets.yml`
4. Run playbook: `ansible-playbook playbook.yml --ask-vault-pass`

**Remember:** Never commit password files, use strong passwords, and organize secrets by environment.