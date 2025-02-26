# argo-events
Experiment with argo events to trigger a workflow upon file upload in an azure blob storage

## Steps
1. Install kubectx, minikube.
1. Run minikube:
```bash
minikube start
```
1. Switch kubectl context: 
```bash
kubectx minikube
```
1. Install argo.
```bash
kubectl create namespace argo
kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.6.4/install.yaml
```
1. Install argo events
```
kubectl create namespace argo-events
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install.yaml
# Install with a validating admission controller
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install-validating-webhook.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml

```
1. Create the following resources: 
```bash
kubectl apply -n argo-events eventbus.yaml

```










1. With the azurite container running, create a blob container.
NOTE: the account name and passord are azurite's defaults

```bash
az storage container create \                                                  
    --name my-container \
    --account-name devstoreaccount1 \
    --account-key Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw== \
    --blob-endpoint http://127.0.0.1:10000/devstoreaccount1
```
1. Apply configuration to minikube:
```bash
kubectl apply -f sensor.yaml && kubectl apply -f azure_blob_event_source.yaml
```
1. Upload blob.
```bash
az storage blob upload \
    --account-name devstoreaccount1 \
    --account-key Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw== \
    --container-name my-container \
    --name my-blob \
    --file /Users/carlosghabrous/Desktop/test.txt \
    --blob-endpoint http://127.0.0.1:10000/devstoreaccount1
```
1. Check.
```bash
kubectl get workflows
```
