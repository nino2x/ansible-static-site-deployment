# Local Docker environment

## Usage

Run in this directory:
```
docker-compose up
```
This will create network `task-net` and build and launch all the containers.

To access them, you can find their IPs using `docker inspect task-net`.

To sum them up:
* `dev_proxy` is the loadbalancer fronting webservers
* `dev_frontend_*` are webservers mounting the `content` directory in repo root and have metrics exposed on `:8080/stub_status`
* `dev_frontend_exporter_*` are [Nginx Prometheus Exporters](https://github.com/nginxinc/nginx-prometheus-exporter) on `:9113` port
* `dev_prometheus` statically targets exporters and serves on `:9090`
* `dev_grafana` serves on `:3000` and preloads Nginx Prometheus Exporter dashboard

## Cleanup

Run `docker-compose down` stop and remove containers and remove the created network. Optionally run `docker container prune -a` to remove images without associated containers, although this will remove all images without associated containers, not just those from this stack.

## Notes and pitfalls

Main differences from the Ansible setup include not fronting Prometheus and Grafana with a reverse proxy and no metrics exporter targeting the loadbalancer.

Except for frontend webserver container's content volume, all other configuration is baked in and containers have to be rebuilt and redeployed after any change to take effect. No data other than web content is persisted either. Container IPs aren't static so they get shuffled around after every redeploy, which means looking them up every time after running them.
