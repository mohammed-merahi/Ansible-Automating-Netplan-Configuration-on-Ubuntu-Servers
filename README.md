network-deploy
Ansible playbook and role to automate Netplan configuration on a single Ubuntu server. This project demonstrates a secure, idempotent workflow using templated YAML, group variables, and Ansible Vault.

Prerequisites
Control node with Ansible installed (v2.9+).

Target Ubuntu host (20.04+) with SSH access.

User on target host has sudo privileges.

Git for version control (optional, but recommended).

Repository Structure
Code
network-deploy/
├── group_vars
│   ├── all
│   │   └── vault.yml
│   └── servers.yml
├── hosts.ini
├── roles
│   └── netplan-config
│       ├── tasks
│       │   └── main.yml
│       └── templates
│           └── netplan.yaml.j2
└── site.yml
group_vars/all/vault.yml – Encrypted file for sensitive data (e.g., ansible_become_pass).

group_vars/servers.yml – Shared network settings (interface, DNS, gateway).

hosts.ini – Inventory listing the Ubuntu server under [servers].

roles/netplan-config/ – Contains the Ansible role:

tasks/main.yml – Renders and applies the Netplan template.

templates/netplan.yaml.j2 – Jinja2 template for /etc/netplan/01-custom.yaml.

site.yml – Top-level playbook that targets servers and invokes the role.

Setup & Usage
Clone the repo

bash
git clone https://github.com/<your-username>/network-deploy.git
cd network-deploy
Encrypt your sudo password

bash
ansible-vault create group_vars/all/vault.yml
# In the editor, add:
# ansible_become_pass: <YourSudoPassword>
Define shared variables in group_vars/servers.yml

yaml
interface_name: ens33
dhcp4: false
gateway4: "192.168.93.2"
nameservers:
  - "8.8.8.8"
  - "8.8.4.4"
Configure inventory in hosts.ini

ini
[servers]
192.168.93.17 ansible_user=ubuntu
Run the playbook

bash
ansible-playbook -i hosts.ini site.yml --ask-vault-pass
Verify on the Ubuntu host:

bash
ip addr show ens33
ping -c3 8.8.8.8
Vault Best Practices
Store vault.yml only under group_vars/all/.

Use --vault-password-file in ansible.cfg for CI/CD automation.

Never commit unencrypted secrets.

Extending This Project
Add multiple hosts via host_vars/<hostname>.yml.

Split inventory into staging.ini and production.ini.

Implement handlers to restart only on template changes.

Integrate with Molecule for role testing.

License
MIT License. See LICENSE for details.
