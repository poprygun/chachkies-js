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
