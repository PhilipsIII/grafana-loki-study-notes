# Notes

## Architecture

![[Grafana_Grafana_Loki_Design.svg]]

### Helm chart version & Software version

|              | Helm chart version                                                                                    | App version |
|--------------|-------------------------------------------------------------------------------------------------------|-------------|
| Grafana Loki | [0.50.0](https://github.com/grafana/helm-charts/tree/loki-distributed-0.50.0/charts/loki-distributed) | 2.5.0       |
| Grafana      | [6.30.3](https://github.com/grafana/helm-charts/tree/grafana-6.30.2/charts/grafana)                   | 8.5.0       |
| Promtail     | [6.0.2](https://github.com/grafana/helm-charts/tree/promtail-6.0.2/charts/promtail)                   | 2.5.0       |

Noted: Grafana Loki's Simple Scalable chart is moved to <https://github.com/grafana/loki/tree/main/production/helm/loki>

- Grafana Loki
  - Simple scalable mode: <https://github.com/grafana/loki/tree/main/production/helm/loki>
  - Microservice mode: <https://github.com/grafana/helm-charts/tree/main/charts/loki-distributed>
- Grafana
  - <https://github.com/grafana/helm-charts/tree/main/charts/grafana>
- Promtail
  - <https://github.com/grafana/helm-charts/tree/main/charts/promtail>

## Install Grafana Loki

```bash
// Search Loki related charts
helm search repo loki 

// Search chart version & app version
helm search repo grafana/loki-distributed --versions

// Download specific Loki version's values
helm show values grafana/loki-distributed --version <version> > values.yaml

// Install Loki in microservice mode
kubectl create namespace loki
helm install loki grafana/loki-distributed -n loki -f ./microservice-mode/values.yaml
```

### Loki(-distributed) notes

- The following pods are up by default `values.yaml`.
  - distributor
  - gateway
  - ingester
  - querier
  - query-frontend

## Install Grafana

```shell
//
helm install grafana grafana/grafana --version 6.30.3 -n loki -f values.yaml

// Get your 'admin' user password by running:
kubectl get secret --namespace loki grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

// Port forward so as to access through http://localhost:8083
k get pods -n loki
k port-forward -n loki <grafana-pod-name> 8083:3000 

```

### Grafana notes

#### add loki as datasource

#### use external database

#### sidecar for dashboard

#### Trouble-shooting

- PodSecurityPolicy Deprecation since kubernetes v1.25
  - `This procedure is only applicable if rbac.create is true and rbac.type is psp in your current Helm values`
  - <https://grafana.com/docs/helm-charts/mimir-distributed/latest/migration-guides/migrate-to-kubernetes-version-1.25/>

```yaml
rbac:
  create: false
  pspEnabled: false
```

## Set-up sample application

- Use [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo/tree/main) as sample application

```bash
// Install by helm
k create namespace onlineboutique
helm install onlineboutique oci://us-docker.pkg.dev/online-boutique-ci/charts/onlineboutique -n onlineboutique -f values.yaml

// Watch the status of the frontend IP address with:
kubectl get --namespace onlineboutique svc -w frontend-external

// Port forward so as to access through http://localhost:8082
kubectl port-forward svc/frontend-external -n onlineboutique 8082:80
```

## Install Promtail

## Other Reference

- <https://ystatit.medium.com/k8s-pv-pvc-and-configmap-for-prometheus-and-grafana-caa044b0d82b>
