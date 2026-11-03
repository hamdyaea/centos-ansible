# Enterprise Linux Maintenance — Ansible Playbooks

Developer: Hamdy Abou El Anein
Contact: hamdy.aea@protonmail.com
License: GNU GPL v3 (see LICENSE).

Modernized maintenance playbooks for **supported Enterprise Linux 8+**
hosts (Rocky Linux, AlmaLinux, RHEL). CentOS Linux 8 itself is end-of-life
and should be migrated to a supported distribution.

## Safety changes

- Uses `ansible.builtin.dnf` with valid YAML and modern FQCNs.
- Runs one host at a time (`serial: 1`) for controlled maintenance.
- **Never automatically promotes a newly generated AIDE baseline**.
  The old scripts copied `aide.db.new.gz` over the trusted database
  without review, potentially concealing malicious file changes.
- The reboot playbook uses `ansible.builtin.reboot` and waits for the
  host to return; rebooting is a separate explicit operation.
- No secrets are stored in the playbooks.

## Syntax-only validation

```sh
ansible-playbook -i localhost, --syntax-check centos-update.yml
ansible-playbook -i localhost, --syntax-check centos-aide.yml
ansible-playbook -i localhost, --syntax-check update+AIDE.yml
ansible-playbook -i localhost, --syntax-check centos-restart.yml
```

**Do not run the playbooks against production machines without
reviewing the inventory, taking backups, planning a maintenance window
and verifying the appropriate privilege escalation.** Syntax validation
does not prove correct operation on a live Enterprise Linux host.

The original playbooks are preserved on Git branch `old`.
