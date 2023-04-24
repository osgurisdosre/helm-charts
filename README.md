## Get Repo Info

```console
helm repo add pagerbeauty https://osgurisdosre.github.io/helm-charts
helm repo update
```
* You can then run `helm search repo pagerbeauty` to see the charts.

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install my-release pagerbeauty/pagerbeauty
```

## Uninstalling the Chart

To uninstall/delete the my-release deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.
