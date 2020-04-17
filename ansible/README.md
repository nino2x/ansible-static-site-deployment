# Remote Ansible deployment

## Prerequisites

Aside from the Ansible installation, this deployment method also assumes:
* remote nodes are running Ubuntu
* an SSH keypair exists on the control node from which this playbook is run
* public keys have been distributed to remote nodes
* `inventory.yml` has been updated with remote node IPs
* there's at least 3 nodes, one for each group in `inventory.ini`
* control node and remote nodes have no network connectivity issues
* any other Ansible specific configuration for connecting is added (such as setting remote user to connect as)

Ports that need to be opened:
* port `22` on remote nodes allows access to control node
* port `80` on loadbalancer and monitoring nodes is open to world (or at least a range of IPs from which they will be accessed)
* port `80` on webserver nodes is open to loadbalancer nodes
* port `9113` on all remote nodes is open to monitoring node IP used in `inventory.ini`

## Usage

Run in this directory:
```
ansible-playbook -i inventory.ini all.yml
```
After deployment website is served from the loadbalacer IP, Prometheus is reachable from `monitoring_ip/prometheus` and Grafana is reachable from `monitoring_ip/grafana`.

To redeploy after any config or content file changes, just run the `ansible-playbook` command again. Redeployment can optionally be scoped to any single group of remote nodes by replacing the `all.yml` file in the command with the group's respective `.yml` file.

Monitoring node should be kept to a single instance, but loadbalancer and webserver nodes can be added or removed independently, in which case `all.yml` playbook should be run to bootstrap new instances and update configs on the rest.

## Notes and pitfalls

There's no cleanup method yet.

Nginx configs are minimal. Mainly needs SSL certificates and server names added and hardened.

All templating and tasks that use IPs/hostnames only use those listed in `inventory.ini`.

Prometheus and Grafana could be split into separate instances with some playbook reworking.
