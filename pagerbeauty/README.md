# PagerBeauty Helm Chart

* PagerBeauty is a concise PagerDuty on-call widget to add to your monitoring dashboard.
* Reference: https://github.com/sergiitk/pagerbeauty

## Get Repo Info

```console
helm repo add pagerbeauty https://osgurisdosre.github.io/helm-charts
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install my-release 
```

## Uninstalling the Chart

To uninstall/delete the my-release deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.


## Configuration

| Parameter                                 | Description                                   | Default                                                 |
|-------------------------------------------|-----------------------------------------------|---------------------------------------------------------|
| `image.repository`                        | Image repository                              | `sergiitk/pagerbeauty`                                       |
| `image.tag`                               | Overrides the PagerBeauty image tag whose default is the chart appVersion  | `latest`                                                      |
| `image.pullPolicy`                        | Image pull policy                             | `IfNotPresent`                                          |
| `service.type`                            | Kubernetes service type                       | `ClusterIP`                                             |
| `service.port`                            | Kubernetes port where service is exposed      | `80`                                                    |
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
                                             

### Example running PagerBeauty using test key in env

* We advise passing the Access Key by Kubernetes secret (or Kubernetes secrets) or Kubernetes ConfigMaps for more security, but to test you can pass by env as shown below:

```yaml
env:
  # Comma-separated list of PagerDuty schedule ids
  # You can find schedule id in the URL of the schedule on PagerDuty website after symbol #
  # For example, schedule https://example.pagerduty.com/schedules#PJ1P5JQ has id PJ1P5JQ
  PAGERBEAUTY_PD_SCHEDULES: "PJ1P5JQ,P538IZH"
  # PagerDuty REST API v2 Access Key (Read-only)
  # Docs: https://support.pagerduty.com/docs/using-the-api
  PAGERBEAUTY_PD_API_KEY: "y_NbAkKc66ryYTWUXYEu"
  # (Optional) Disable polling for active incidents.
  # Default: false
  PAGERBEAUTY_INCIDENTS_DISABLE: "true"
  # (Optional) The port for HTTP server to listen on.
  # Default: 8080
  PAGERBEAUTY_HTTP_PORT: 80
```