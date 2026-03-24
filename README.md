# Helm charts

## Prerequisites

1. [Install Gateway API resources](https://gateway-api.sigs.k8s.io/guides/getting-started/#install-standard-channel)
2. [Install Gateway controller (NGINX Gateway Fabric)](https://docs.nginx.com/nginx-gateway-fabric/install/helm/#install-from-the-oci-registry)
3. [Set up ECR secrets in Minikube using registry-creds plugin](https://minikube.sigs.k8s.io/docs/tutorials/configuring_creds_for_aws_ecr/)

## How to apply charts

Run `helm install secret-society charts/secret-society --namespace {namespace}`

To upgrade existing chart run `helm upgrade secret-society charts/secret-society --namespace {namespace}`

The `core` Deployment expects a Kubernetes secret named `core-secret` (default value).
This secret is not created by Helm and must exist before installing the chart.

Create it by running:

```bash
kubectl create secret generic core-secret \
  --from-literal=DJANGO_SECRET_KEY='' \
  --from-literal=DB_NAME='' \
  --from-literal=DB_USER='' \
  --from-literal=DB_PASSWORD='' \
  -n <namespace>
```

## To create an initial daily passcode

```bash
kubectl -n <namespace> exec -it deploy/core -- python manage.py shell
```

and then

```python
from app.services import PasscodeService
PasscodeService.update("test")
```
