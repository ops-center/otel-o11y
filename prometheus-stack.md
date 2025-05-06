helm upgrade -i kube-prometheus-stack kube-prometheus-stack \
  --repo https://prometheus-community.github.io/helm-charts \
  --namespace monitoring --create-namespace \
  --set grafana.defaultDashboardsEnabled=false
