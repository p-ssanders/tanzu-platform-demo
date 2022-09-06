#   TCE Platform Setup

This setup assumes that a [TCE management cluster](https://tanzucommunityedition.io/docs/v0.12/getting-started/) has been created.

The commands assume the present working directory to be the root directory of the repository.

Please update the configuration files as necessary or relevant.

##  Deploy Workload Cluster

```sh
tanzu cluster create dev-team -n dev-environments -f tce/app-team-development.yaml
tanzu cluster scale dev-team -w 3 -n dev-environments
```

##  Add Package Repository

```
tanzu package repository add tce-repo --url projects.registry.vmware.com/tce/main:0.12.0 --namespace tanzu-package-repo-global
```

##  Install Packages

```sh
tanzu package install contour --package-name contour.community.tanzu.vmware.com --version 1.20.1

k create ns external-dns
k apply -f external-dns/iam-credentials-secret.yaml
tanzu package install external-dns --package-name external-dns.community.tanzu.vmware.com --version 0.10.0 --values-file external-dns/values.yaml

k create ns cert-manager
k apply -f cert-manager/iam-credentials-secret.yaml
tanzu package install cert-manager --package-name cert-manager.community.tanzu.vmware.com --version 1.8.0
k apply -f cert-manager/cluster-issuer-staging.yaml
k apply -f cert-manager/cluster-issuer-prod.yaml

tanzu package install harbor --package-name harbor.community.tanzu.vmware.com --version 2.4.2 --values-file harbor/harbor-values-secret.yaml
# reconciliation will fail, it's okay
k apply -f harbor/packageinstall-overlay.yaml
# now delete harbor pods to retrigger the reconciliation

# Harbor Setup
#   login to harbor as admin
#   create kpack project
#   create cartographer project

tanzu package install kpack --package-name kpack.community.tanzu.vmware.com --version 0.5.3 -f kpack/kpack-values-secret.yaml

tanzu package install kpack-dependencies --package-name kpack-dependencies.community.tanzu.vmware.com --version 0.0.27 -f kpack/kpack-dependencies-values.yaml

tanzu package install fluxcd-source-controller --package-name fluxcd-source-controller.community.tanzu.vmware.com --version 0.21.5

tanzu package install fluxcd-helm-controller --package-name helm-controller.fluxcd.community.tanzu.vmware.com --version 0.17.2

tanzu package install fluxcd-kustomize-controller -p kustomize-controller.fluxcd.community.tanzu.vmware.com -v 0.21.1 --values-file fluxcd/kustomize/kustomize-controller-values.yaml

tanzu package install cartographer --package-name cartographer.community.tanzu.vmware.com --version 0.3.0

tanzu package install knative --package-name knative-serving.community.tanzu.vmware.com --version 1.0.0 --values-file knative/values.yaml

tanzu package install cartographer-catalog --package-name cartographer-catalog.community.tanzu.vmware.com --version 0.3.0 --values-file cartographer/catalog-values.yaml

k apply -f fluxcd/helm/bitnami-helm-repository.yaml
k apply -f fluxcd/kustomize/git-repository.yaml
k apply -f fluxcd/kustomize/kustomizer.yaml
```


