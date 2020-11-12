# Angular application with Concourse CI

## Prerequisites

[Install Concourse](https://tanzu.vmware.com/developer/guides/ci-cd/concourse-gs/)

```bash
fly -t chackies-target login -c http://127.0.0.1:8080
fly -t chackies-target set-pipeline -c ci/pipeline.yml -p chachkies-pipeline -l ci/credentials.yml
fly -t chackies-target unpause-pipeline -p chachkies-pipeline
```

Trigger job

```bash
fly -t chackies-target trigger-job -j chachkies-pipeline/publish -w
```

=======
# Check all possible clusters, as your .KUBECONFIG may have multiple contexts:
kubectl config view -o jsonpath='{"Cluster name\tServer\n"}{range .clusters[*]}{.name}{"\t"}{.cluster.server}{"\n"}{end}'

# Select name of cluster you want to interact with from above output:
export CLUSTER_NAME="docker-desktop"

# Point to the API server referring the cluster name
APISERVER=$(kubectl config view -o jsonpath="{.clusters[?(@.name==\"$CLUSTER_NAME\")].cluster.server}")

# Gets the token value
TOKEN=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)

# Explore the API with TOKEN
curl -X GET $APISERVER/api --header "Authorization: Bearer $TOKEN" --insecure
