# Helm charts

## Prerequisites

1. [Install Gateway API resources](https://gateway-api.sigs.k8s.io/guides/getting-started/#install-standard-channel)
2. [Install Gateway controller (NGINX Gateway Fabric)](https://docs.nginx.com/nginx-gateway-fabric/install/helm/#install-from-the-oci-registry)
3. [Set up ECR secrets in Minikube using registry-creds plugin](https://minikube.sigs.k8s.io/docs/tutorials/configuring_creds_for_aws_ecr/)

## How to apply charts

### Notes

The `email` Deployment expects a Kubernetes secret named `email-secret` (value that can be changed in values.yaml).
This secret is not created by Helm and must exist before installing the chart.

Create it by running:

```bash
kubectl create secret generic email-secret \
  --from-literal=DJANGO_SECRET_KEY='' \
  --from-literal=SMTP_USER='' \
  --from-literal=SMTP_PASS='' \
  -n <namespace>
```

### Applying charts

Run `helm install secret-society charts/secret-society --namespace {namespace}`

To upgrade existing chart run `helm upgrade secret-society charts/secret-society --namespace {namespace}`
