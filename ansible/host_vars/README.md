# Host vars

Place per-host vaulted variable files in `host_vars/<hostname>/vault.yml`.
Example:

  host_vars/app-prod-01.example.com/vault.yml

Encrypt host vaults with:

  ansible-vault encrypt host_vars/<host>/vault.yml

Keep secrets only in encrypted files.