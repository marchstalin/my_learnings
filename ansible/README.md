# Ansible provisioning and deployment

This folder contains inventories, vaulted variables, roles, and playbooks to provision infrastructure and CI systems (Prometheus, Jenkins, GitLab, GitLab Runners), plus Docker-based deployments for GitLab, Jenkins, and Nexus.

## Inventory structure

Inventories are split per environment and deployment type using flat filenames:

- `ansible/inventory/dev-app.yml`
- `ansible/inventory/dev-infra.yml`
- `ansible/inventory/uat-app.yml`
- `ansible/inventory/uat-infra.yml`
- `ansible/inventory/prod-app.yml`
- `ansible/inventory/prod-infra.yml`
- `ansible/inventory/amx-app.yml`
- `ansible/inventory/amx-infra.yml`

App inventories define Docker Swarm topology using:

- `swarm_managers`
- `swarm_workers`

Infra inventories define monitoring hosts in the `monitoring` group.

## Current playbooks

- `ansible/playbooks/site.yml`
  - loads secrets using `hashicorp_vault`
  - provisions Prometheus on `monitoring`
  - bootstraps Docker Swarm and deploys CI roles on `app`
- `ansible/playbooks/gitlab_docker.yml`
  - deploys GitLab CE in Docker
- `ansible/playbooks/jenkins_docker.yml`
  - deploys Jenkins in Docker
- `ansible/playbooks/nexus_docker.yml`
  - deploys Nexus Repository Manager in Docker

## Docker Swarm support

The `docker_swarm` role is included in `site.yml` and uses the app inventory group definitions above.
It installs Docker on all nodes, initializes the swarm on the first manager, and joins workers automatically.

## Vault and secrets

- Create or update `group_vars/all/vault.yml` and per-host vaulted files in `host_vars/`.
- Encrypt secret files with `ansible-vault encrypt <file>`.
- Provide a vault password file or use `--ask-vault-pass` when running playbooks.

Example: encrypt and run infra or app playbooks

```bash
ansible-vault encrypt ansible/group_vars/all/vault.yml
ansible-playbook -i ansible/inventory/uat-infra.yml ansible/playbooks/site.yml --ask-become-pass --ask-vault-pass
ansible-playbook -i ansible/inventory/uat-app.yml ansible/playbooks/site.yml --ask-become-pass --ask-vault-pass
```

Notes

- Replace placeholder values in vaulted files before running.
- Runner registration uses `gitlab_runner_registration_token` from vaulted vars.
- Nexus credentials are stored in `group_vars/all/vault.yml` as `nexus_admin_user` and `nexus_admin_password`.

## Docker collection

These Docker playbooks use the `community.docker` collection. Install it with:

```bash
ansible-galaxy collection install community.docker
```

Ensure Docker is installed on the target hosts and that the target can run containers.

## Nexus notes

- `ansible/playbooks/nexus_docker.yml` deploys `sonatype/nexus3` for artifact hosting.
- Nexus writes an initial admin password to `/nexus-data/admin.password` on first startup. To automate admin password setting or rotation, use the Nexus REST API after the container is healthy.
