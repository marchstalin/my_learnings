# Docker Swarm Role

This role prepares a 3-node Docker Swarm cluster using manager and worker hosts.

Groups:
- `swarm_managers`
- `swarm_workers`

Inventory:
- `*-app.yml` should define `swarm_managers` and `swarm_workers` within the app environment.

Behavior:
- Installs Docker and Python Docker bindings on all nodes.
- Initializes the swarm on the first manager host.
- Registers workers using the manager join token.

Ensure your host inventory defines the manager host before worker hosts in `swarm_managers`.
