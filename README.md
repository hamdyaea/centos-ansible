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

## Explicit maintenance approval

The repository inventory contains **no active hosts**. Copy `hosts` to a
private inventory and configure only the systems you administer. A previously
committed host address has been removed from the current source branch; note
that Git history may still contain it.

Every playbook refuses to proceed unless `maintenance_approved=true` is
explicitly supplied. Approval does not replace a maintenance window,
backup verification or host-by-host review. For an approved run only:

```sh
ansible-playbook -i /path/to/private-inventory -e maintenance_approved=true centos-update.yml
```

**Do not execute this command without an approved change window.** The
update and reboot playbooks can change a system. Syntax checks do not
execute maintenance tasks.

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