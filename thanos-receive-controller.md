- https://github.com/observatorium/thanos-receive-controller


cat <<'EOF' | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: thanos-receive
  labels:
    app.kubernetes.io/name: thanos-receive
data:
  hashrings.json: |
    [
        {
            "hashring": "hashring0",
            "tenants": ["foo", "bar"]
        },
        {
            "hashring": "hashring1",
            "tenants": ["baz"]
        }
    ]
EOF



cat <<'EOF' | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: thanos-receive
  labels:
    app.kubernetes.io/name: thanos-receive
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  verbs:
  - get
  - list
  - watch
  - create
  - update
  - patch
  - delete
- apiGroups:
  - apps
  resources:
  - statefulsets
  verbs:
  - get
  - list
  - watch
  - create
  - update
  - patch
  - delete
EOF



cat <<'EOF' | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: thanos-receive
  labels:
    app.kubernetes.io/name: thanos-receive
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: thanos-receive
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
EOF



cat <<'EOF' | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: thanos-receive-controller
  labels:
    app.kubernetes.io/name: thanos-receive-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: thanos-receive-controller
  template:
    metadata:
      labels:
        app.kubernetes.io/name: thanos-receive-controller
    spec:
      containers:
      - args:
        - --configmap-name=thanos-receive
        - --configmap-generated-name=thanos-receive-generated
        - --file-name=hashrings.json
        image: quay.io/observatorium/thanos-receive-controller
        name: thanos-receive-controller
EOF
