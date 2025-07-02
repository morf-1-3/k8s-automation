# Deployment repository

## Description

This repository is a part of a DevOps ToDo application project. It stores helm charts and deploys new versions into a cluster through CI/CD pipeline.

## Overview

Helm charts organized using `helmfile`. The entire application can be launched by doing single `helmfile apply` command.

The CI/CD pipeline runs static analyzers (`yamllint`, `helm lint`, `helmfile template`, `kubeconform`) to validate yaml files and charts. Upon success it applies the changes to the cluster through service account. The `KUBECONFIG` project variable stores `kubectl` configuration for the service account, including token and certificate. This pipeline can be triggered from a ToDo application source code repository for automatic deployment of new versions.

Sensetive data is stored in encrypted format and can be decrypted using private `age` keys. One `age` key is stored in a `SOPS_AGE_KEY` project variable to access encrypted data from the pipeline using `sops` utility. This utility also allows automatic decryption of the data in helm charts using `helm secrets` plugin.

### Dependencies

In summary, this project needs:

- **helm** for installing and updating charts
- **age** and **sops** for encryption
- **helm secrets** plugin to integrate sops into helm
- **helmfile** for managing helm charts

### Notes

It is not guaranteed that during initial setup traefik CRDs will be installed first. That's why it is recommended to run this command during initial setup before helmfile apply.

```bash
helmfile -f helmfiles/infra.yaml apply --selector name=traefik-crds
```
