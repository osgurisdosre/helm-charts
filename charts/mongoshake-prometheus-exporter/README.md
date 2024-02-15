# MongoShake Prometheus Exporter Helm Chart

* MongoShake Prometheus Exporter is an exporter that adapts MongoShake metrics to the Prometheus format, making it easier for administrators to monitor resources..
* Reference: [MongoShake Prometheus Exporter](https://github.com/osgurisdosre/mongoshake-prometheus-exporter)

## Get Repo Info

```console
helm repo add mongoshake-prometheus-exporter https://osgurisdosre.github.io/helm-charts
helm repo update
```
* You can then run `helm search repo mongoshake-prometheus-exporter` to see the charts.

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install mongoshake-prometheus-exporter mongoshake-prometheus-exporter/mongoshake-prometheus-exporter
```

## Uninstalling the Chart

To uninstall/delete the my-release deployment:

```console
helm delete mongoshake-prometheus-exporter
```

The command removes all the Kubernetes components associated with the chart and deletes the release.


## Configuration

| Parameter                                 | Description                                   | Default                                                 |
|-------------------------------------------|-----------------------------------------------|---------------------------------------------------------|
| `image.repository`                        | Image repository                              | `sergiitk/pagerbeauty`                                       |
| `image.tag`                               | Overrides the PagerBeauty image tag whose default is the chart appVersion  | `latest`                                                      |
| `image.pullPolicy`                        | Image pull policy                             | `IfNotPresent`                                          |
| `service.type`                            | Kubernetes service type                       | `ClusterIP`                                             |
| `service.port`                            | Kubernetes port where service is exposed      | `8000`                                                    |
| `ingress.enabled`                         | Enables Ingress                               | `false`                                                 |
| `ingress.annotations`                     | Ingress annotations (values are templated)    | `{}`                                                    |
| `ingress.path`                            | Ingress accepted path                         | `/`                                                     |
| `ingress.hosts`                           | Ingress accepted hostnames                    | `["chart-example.local"]`                                                    |
| `ingress.tls`                             | Ingress TLS configuration                     | `[]`                                                    |
| `ingress.className`                       | Ingress Class Name. MAY be required for Kubernetes versions >= 1.18 | `""`                              |
| `resources`                               | CPU/Memory resource requests/limits           | `{}`                                                    |
| `nodeSelector`                            | Node labels for pod assignment                | `{}`                                                    |
| `tolerations`                             | Toleration labels for pod assignment          | `[]`                                                    |
| `affinity`                                | Affinity settings for pod assignment          | `{}`                                                    |
| `env`                                     | Extra environment variables passed to pods    | `{}`                                                    |
| `envFromSecret`                           | Name of a Kubernetes secret (must be manually created in the same namespace) containing values to be added to the environment. Can be templated | `""` |
| `envFromSecrets`                          | List of Kubernetes secrets (must be manually created in the same namespace) containing values to be added to the environment. Can be templated | `[]` |
| `envFromConfigMaps`                       | List of Kubernetes ConfigMaps (must be manually created in the same namespace) containing values to be added to the environment. Can be templated | `[]` |
| `podAnnotations`                          | Pod annotations                               | `{}`                                                    |
| `serviceAccount.annotations`              | ServiceAccount annotations                    |                                                         |
| `serviceAccount.create`                   | Create service account                        | `true`                                                  |
| `serviceAccount.name`                     | Service account name to use, when empty will be set to created account if `serviceAccount.create` is set else to `default` | `` |
| `serviceMonitor.enabled`                  | Enables the creation of a service monitor used later for Prometheus discovery. When empty, it will be set to `false`. | `false` |
                                             

### Example

For a demonstration, we used a MongoShake instance operating in Kubernetes.

```bash
curl http://mongoshake.monitoring.svc.cluster.local:9100/repl | jq
```

We get the following response:

```json
{
  "who": "mongoshake",
  "tag": "",
  "replset": "mongoshake-shard-0",
  "logs_get": 786,
  "logs_repl": 62,
  "logs_success": 62,
  "tps": 0,
  "lsn": {
    "unix": 1707334793,
    "time": "2024-02-07 19:39:53",
    "ts": "7332947099257929731"
  },
  "lsn_ack": {
    "unix": 1707334793,
    "time": "2024-02-07 19:39:53",
    "ts": "7332947099257929731"
  },
  "lsn_ckpt": {
    "unix": 1707334656,
    "time": "2024-02-07 19:37:36",
    "ts": "7332946510847410178"
  },
  "now": {
    "unix": 1707334864,
    "time": "2024-02-07 19:41:04"
  },
  "log_size_avg": "503.00B",
  "log_size_max": "12.29KB"
}
```

Installing the Chart
```bash
helm repo add mongoshake-prometheus-exporter https://osgurisdosre.github.io/helm-charts
helm repo update
helm install my-release mongoshake-prometheus-exporter/mongoshake-prometheus-exporter -f values -n monitoring
```
In the values file we used the following settings:
```yaml
image:
  repository: ghcr.io/osgurisdosre/mongoshake-prometheus-exporter
  pullPolicy: IfNotPresent
  tag: "latest"

env:
  URL_LIST: "http://mongoshake.monitoring.svc.cluster.local:9100/repl"
  SCRAPE_INTERVAL: 5

service:
  type: ClusterIP
  port: 8000
  labels:
    release: prometheus

serviceMonitor:
  enabled: true
  path: /metrics
  interval: 10s
  namespace: monitoring
  labels: 
    release: prometheus

```
Now we can check the output by the service created

```bash
curl http://mongoshake-prometheus-exporter.monitoring.svc.cluster.local:8000/metrics/
```

The following response is obtained:
```bash
# HELP mongoshake_logs_get Number of logs (get)
# TYPE mongoshake_logs_get gauge
mongoshake_logs_get{replset="Release-shard-0",url="http://mongoshake.monitoring.svc.cluster.local:9100/repl"} 44736.0
# HELP mongoshake_logs_repl Number of logs (repl)
# TYPE mongoshake_logs_repl gauge
mongoshake_logs_repl{replset="Release-shard-0",url="http://mongoshake.monitoring.svc.cluster.local:9100/repl"} 1109.0
# HELP mongoshake_logs_success Number of successful logs
# TYPE mongoshake_logs_success gauge
mongoshake_logs_success{replset="Release-shard-0",url="http://mongoshake.monitoring.svc.cluster.local:9100/repl"} 1109.0
# HELP mongoshake_tps Transactions per second
# TYPE mongoshake_tps gauge
mongoshake_tps{replset="Release-shard-0",url="http://mongoshake.monitoring.svc.cluster.local:9100/repl"} 0.0
# HELP mongoshake_replication_latency Replication_latency in MS
# TYPE mongoshake_replication_latency gauge
mongoshake_replication_latency{replset="Release-shard-0",url="http://mongoshake.monitoring.svc.cluster.local:9100/repl"} 0.0
```

Note that the metrics are now being exported in Prometheus format.

### Contributions

Contributions are welcome! If you wish to contribute, please:
- Fork the repository;

- Create a new branch for your feature or fix;

- Send a pull request with your proposal.

To report bugs or request new features, use the issues section of the repository.
