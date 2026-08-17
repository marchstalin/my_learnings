# HashiCorp Vault role

This role provides a central place to load secrets for other roles.

Behavior
- When `use_hashicorp_vault: false` (default) it relies on vaulted Ansible variables (for example `group_vars/all/vault.yml`). Those variables are loaded by Ansible automatically and available to plays/roles.
- When `use_hashicorp_vault: true` this role will install the `hvac` Python library. You should implement the secret fetch logic in `tasks/main.yml` using the `community.hashi_vault` lookup or the `hvac` Python client.

Example to fetch a secret with the lookup plugin (add to `tasks/main.yml`):

```yaml
- name: Read GitLab root password from Vault
  set_fact:
    gitlab_root_password: "{{ lookup('community.hashi_vault.hashivault', 'secret=secret/data/gitlab field=data.password url=' + vault_addr + ' token=' + vault_token) }}"
```

Keep `vault_token` and `vault_addr` in vaulted files or use environment-based authentication.

Usage
- Add this role to the top of `playbooks/site.yml` or add it as a dependency in role `meta/main.yml`.