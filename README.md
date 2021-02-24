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
  -e "DATAHUB_TOKEN=<your Data Hub token>" \
  docker.io/grafana/grafana
```

Point your web browser at http://localhost:3000 and login with
admin/admin.

### Verify the Data Hub data source

In Grafana, click Configuration (gear icon on left) -> Data Sources,
select the Data Hub source, and click the Test button at the
bottom. If the test doesn't work, confirm you are connected to the VPN
and that your login token (and just the token - not the whole oc login
command) are set in the DATAHUB_TOKEN environment variable.

### Run a test query

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