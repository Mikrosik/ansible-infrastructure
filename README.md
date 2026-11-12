# Ansible infrastructure playbooks

These playbooks install and enable Nginx and Docker, and manage explicitly
configured user accounts on Ubuntu and Astra Linux hosts. Both distributions
use Ansible's Debian-family package module; Docker is installed from the
distribution's `docker.io` package.

## Requirements

- Ansible Core on the control machine
- SSH access to the target hosts
- `ansible.posix` collection for SSH authorized-key management:
  `ansible-galaxy collection install ansible.posix`
- A target account with sudo access

## Configure inventory

Edit `inventory/hosts.ini` and replace the example address and SSH user with
your own. The address `192.0.2.10` is reserved for documentation and is not a
real server.

## Configure users

Set `managed_users` in `group_vars/all.yml` (or an environment-specific
`group_vars` file) to the accounts you want Ansible to ensure exist. For
example:

```yaml
managed_users:
  - name: deploy
    comment: Deployment account
    groups:
      - sudo
    ssh_public_key: "ssh-ed25519 AAAA... replace-with-your-public-key"
```

Only public SSH keys belong here; do not store passwords or private keys in
inventory. Omit `groups` if no supplementary groups should be configured.
`docker_users` is empty by default. Membership in the Docker group grants
root-equivalent access, so list accounts there only when that privilege is
intended.

## Run

Check connectivity and configuration first:

```sh
ansible all -m ansible.builtin.ping
ansible-playbook --syntax-check playbooks/site.yml
```

Run all playbooks or an individual playbook from this directory:

```sh
ansible-playbook playbooks/site.yml --ask-become-pass
ansible-playbook playbooks/nginx.yml --ask-become-pass
ansible-playbook playbooks/docker.yml --ask-become-pass
ansible-playbook playbooks/users.yml --ask-become-pass
```
