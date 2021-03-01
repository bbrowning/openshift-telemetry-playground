# OpenShift Telemetry Playground

## Quickstart

### Get your Data Hub token

Follow steps at
https://help.datahub.redhat.com/docs/interacting-with-telemetry-data
(VPN required) under "Programmatic access" to get a personal token to
authenticate with Prometheus.

### Run a Grafana container

Clone this repository locally, cd into that directory, then run:

```
podman run --name=grafana -p 3000:3000 \
  --security-opt label=disable \
  -v $PWD/config:/etc/grafana/provisioning \
  -v $PWD/dashboards:/var/lib/grafana/dashboards \
  -v $PWD/plugins:/var/lib/grafana/plugins \
  -e "DATAHUB_TOKEN=<your Data Hub token>" \
  docker.io/grafana/grafana:7.4.3
```

If you get an error like `"container init caused: rootfs_linux.go:59: mounting \"sysfs\" to rootfs at \"/sys\" caused: operation not permitted": OCI permission denied"` when running the above command and you're using podman 3.x, make you need to `dnf install crun` and try again.

### Verify the Data Hub data source

Point your web browser at http://localhost:3000 and login with
admin/admin.

In Grafana, click Configuration (gear icon on left) -> Data Sources,
select the Data Hub source, and click the Test button at the
bottom. If the test doesn't work, confirm you are connected to the VPN
and that your login token (and just the token - not the whole oc login
command) are set in the DATAHUB_TOKEN environment variable.

### Explore the default dashboards

After logging in, head to http://localhost:3000/dashboards to try one
of the default dashboards.

### Run a custom query

Go to the Explore page (compass on left hand side) and run a test query, such as:

```
subscription_sync_total{installed=~"serverless-operator.*"}
```

You'll likely need to adjust the time range to look a few hours in the
past to get any data returned.

### To shutdown

If running in the foreground, just `CTRL+C` the process. Otherwise:

```
podman stop grafana
```

### To start again

```
podman start grafana
```


### To delete the container and start fresh

After shutting down Grafana:

```
podman rm grafana
```
