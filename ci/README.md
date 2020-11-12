# Angular application with Concourse CI

## Prerequisites

[Install Concourse](https://tanzu.vmware.com/developer/guides/ci-cd/concourse-gs/)

```bash
fly -t chackies-target login -c http://127.0.0.1:8080
```

## Define your own credentials in `credentials.yml`

docker-hub-username: __docker-user__
docker-hub-password: __docker-password__

## Select name of cluster you want to interact with from above output

```bash
export CLUSTER_NAME="docker-desktop"
```

## Point to the API server referring the cluster name

```bash
APISERVER=$(kubectl config view -o jsonpath="{.clusters[?(@.name==\"$CLUSTER_NAME\")].cluster.server}")
echo $APISERVER
```

Docker Desktop Kubernetes API server is `https://kubernetes.docker.internal:6443`

server: $APISERVER

namespace: default

## Obtain from ~/.kube/config - clusters.cluster.certificate-authority-data

cad: $CERTIFICATE_AUTHORITY_DATA

## Gets the token value

```bash
TOKEN=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)
echo $TOKEN
```
token: $TOKEN

## Configure pipeline

```bash
fly -t chackies-target set-pipeline -c ci/pipeline.yml -p chachkies-pipeline -l ci/credentials.yml
```

## Trigger push to Dockerhub job

```bash
fly -t chackies-target trigger-job -j chachkies-pipeline/publish -w
```

## Trigger push to k8s job

```bash
fly -t chackies-target trigger-job -j chachkies-pipeline/run-chachkies-in-k8s -w
```
