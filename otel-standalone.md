kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.17.1/cert-manager.yaml

kubectl get ns kube-system -o=jsonpath='{.metadata.uid}'

helm install kubedb oci://ghcr.io/appscode-charts/kubedb \
  --version v2025.4.30 \
  --namespace kubedb --create-namespace \
  --set global.featureGates.ClickHouse=true \
  --set-file global.license=license.txt \
  --wait --burst-limit=10000 --debug

helm upgrade -i appscode-otel-stack oci://ghcr.io/appscode-charts/appscode-otel-stack \
  --version v2025.2.28 \
  --namespace monitoring --create-namespace

