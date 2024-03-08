## Install Grafana Loki

- Helm-charts 
  - Simple scalable mode: https://github.com/grafana/loki/tree/main/production/helm/loki
  - Microservice mode: https://github.com/grafana/helm-charts/tree/main/charts/loki-distributed

### commands
```bash
// Search Loki related charts
helm search repo loki 

// Download specific Loki version's values
helm show values grafana/loki-simple-scalable --version <version> > values.yaml

// Install Loki in microservice mode
helm install loki grafana/loki-distributed -n <namespace> -f <values.yaml>
```

### Loki(-distributed) notes
- helm chart version: 0.50.0
  - loki version: 2.5.0
- The following pods are up by default `values.yaml`.
  - distributor
  - gateway
  - ingester
  - querier
  - query-frontend

## Install Grafana
(TBD)

## Set-up sample application
- Use [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo/tree/main) sample application

### commands
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
