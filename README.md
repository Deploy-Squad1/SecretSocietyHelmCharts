# Helm charts

## Prerequisites

1. [Install Gateway API resources](https://gateway-api.sigs.k8s.io/guides/getting-started/#install-standard-channel)

2. [Install Gateway controller (NGINX Gateway Fabric)](https://docs.nginx.com/nginx-gateway-fabric/install/helm/#install-from-the-oci-registry)

## How to apply charts

Run `helm install {chart_name} {chart_folder_path} --namespace {namespace}`

To upgrade existing chart run `helm upgrade {chart_name} {chart_folder_path} --namespace {namespace}`
