# Helm charts

## Prerequisites

1. [Install Consul on Kubernetes](https://github.com/hashicorp/consul-k8s.git) using client-values in consul-values folder
2. (For local testing) [Set up ECR secrets in Minikube using registry-creds plugin](https://minikube.sigs.k8s.io/docs/tutorials/configuring_creds_for_aws_ecr/)

## How to apply charts

### Notes

- The `core` Deployment expects a Kubernetes secret named `core-secret` (default value).
Create it by running:

```bash
kubectl create secret generic core-secret \
  --from-literal=DJANGO_SECRET_KEY='' \
  --from-literal=DB_NAME='' \
  --from-literal=DB_USER='' \
  --from-literal=DB_PASSWORD='' \
  -n <namespace>
```

- The `email` Deployment expects a Kubernetes secret named `email-secret` (value that can be changed in values.yaml).
Create it by running:

```bash
kubectl create secret generic email-secret \
  --from-literal=DJANGO_SECRET_KEY='' \
  --from-literal=SMTP_USER='' \
  --from-literal=SMTP_PASS='' \
  -n <namespace>
```

- The `map` Deployment expects a Kubernetes secret named `map-secret` (default value).
Create it by running:

```bash
kubectl create secret generic map-secret \
  --from-literal=JWT_SECRET='' \
  --from-literal=DB_NAME='' \
  --from-literal=DB_USER='' \
  --from-literal=DB_PASSWORD='' \
  --from-literal=AWS_ACCESS_KEY_ID='' \
  --from-literal=AWS_SECRET_ACCESS_KEY='' \
  --from-literal=AWS_BUCKET_NAME='' \
  -n <namespace>
```

- The `voting` Deployment expects a Kubernetes secret named `voting-secret` (default value).
Create it by running:

```bash
kubectl create secret generic voting-secret \
  --from-literal=JWT_SECRET='' \
  --from-literal=DB_NAME='' \
  --from-literal=DB_USER='' \
  --from-literal=DB_PASSWORD='' \
  -n <namespace>
```

### Applying charts

```bash
helm install secret-society charts/secret-society \
  --namespace {namespace} \
  --f charts/secret-society/env/values.yaml \
  --f charts/secret-society/env/values-{environment}.yaml
```

To upgrade existing chart run the following command:

```bash
helm upgrade secret-society charts/secret-society \
  --namespace {namespace} \
  --f charts/secret-society/env/values.yaml \
  --f charts/secret-society/env/values-{environment}.yaml
```
