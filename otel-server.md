kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.17.1/cert-manager.yaml

kubectl get ns kube-system -o=jsonpath='{.metadata.uid}'

helm install kubedb oci://ghcr.io/appscode-charts/kubedb \
  --version v2025.4.30 \
  --namespace kubedb --create-namespace \
  --set global.featureGates.ClickHouse=true \
  --set-file global.license=license.txt \
  --wait --burst-limit=10000 --debug


### Install Minio:

helm upgrade --install minio operator \
  --repo https://operator.min.io/ \
  --namespace minio-operator --create-namespace \
  --set operator.replicaCount=1 \
  --wait

helm upgrade --install minio-tenant tenant \
  --repo https://operator.min.io/ \
  --namespace minio --create-namespace \
  --set tenant.pools[0].servers=1 \
  --set tenant.pools[0].volumesPerServer=1 \
  --set tenant.pools[0].size=1Gi \
  --set tenant.pools[0].name="default" \
  --set tenant.buckets[0].name="test" \
  --wait

k port-forward -n minio svc/myminio-console 9443:9443

- Access Key: `minio`
- Secret Key: `minio123`
- Endpoint: `https://localhost:9443`


helm upgrade -i opentelemetry-kube-stack opentelemetry-kube-stack \
--repo https://open-telemetry.github.io/opentelemetry-helm-charts \
-n monitoring --create-namespace \
--version 0.5.2


### Deploy Thanos

#### Create Namespace

```bash
kubectl create namespace thanos
```

#### Deploy Thanos

```bash
kubectl -n thanos create secret generic thanos-objstore-config \
  --from-file=objstore.yml=./thanos/s3.yaml
```

```bash
helm upgrade -i thanos oci://registry-1.docker.io/bitnamicharts/thanos \
  --version 16.0.4 \
  --namespace thanos --create-namespace \
  --values=./thanos/values.yaml
```
