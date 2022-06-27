#   TCE Cluster Setup

##  Deploy Clusters

```
tanzu management-cluster create --ui
tanzu cluster create tce-demo-workload-0 --file tce-demo-workload-0.yaml
```

##  Upgrade Clusters

```
AWS_REGION=us-west-2 tanzu management-cluster upgrade --os-name amazon --os-version 2 --os-arch amd64
tanzu cluster upgrade tce-demo-workload-0
```

##  Add Package Repository

```
tanzu package repository add tce-repo --url projects.registry.vmware.com/tce/main:0.12.0 --namespace tanzu-package-repo-global
```

##  Install Packages

```
tanzu package install contour --package-name contour.community.tanzu.vmware.com --version 1.20.1

k create ns external-dns
k apply -f external-dns/iam-credentials-secret.yaml
tanzu package install external-dns --package-name external-dns.community.tanzu.vmware.com --version 0.10.0 --values-file external-dns/external-dns-config.yaml

k create ns cert-manager
k apply -f cert-manager/iam-credentials-secret.yaml
tanzu package install cert-manager --package-name cert-manager.community.tanzu.vmware.com --version 1.6.3
k apply -f cert-manager/cluster-issuer-staging.yaml
k apply -f cert-manager/cluster-issuer-prod.yaml

tanzu package install harbor --package-name harbor.community.tanzu.vmware.com --version 2.4.2 --values-file harbor/harbor-values-secret.yaml
k apply -f harbor/packageinstall-overlay.yaml

tanzu package install kpack --package-name kpack.community.tanzu.vmware.com --version 0.5.3 -f kpack/kpack-values.yaml

tanzu package install kpack-dependencies --package-name kpack-dependencies.community.tanzu.vmware.com --version 0.0.27 -f kpack/kpack-dependencies-values.yaml

tanzu package install fluxcd-source-controller --package-name fluxcd-source-controller.community.tanzu.vmware.com --version 0.21.5

tanzu package install fluxcd-helm-controller --package-name helm-controller.fluxcd.community.tanzu.vmware.com --version 0.17.2

tanzu package install fluxcd-kustomize-controller -p kustomize-controller.fluxcd.community.tanzu.vmware.com -v 0.21.1 --values-file fluxcd/kustomize-controller-values.yaml

tanzu package install cartographer --package-name cartographer.community.tanzu.vmware.com --version 0.3.0

k apply -f fluxcd/helm/bitnami-helm-repository.yaml
k apply -f fluxcd/kustomize/git-repository.yaml
k apply -f fluxcd/kustomize/kustomizer.yaml
```


