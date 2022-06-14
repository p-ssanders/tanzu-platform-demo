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

##  Install Packages

```
tanzu package install contour --package-name contour.community.tanzu.vmware.com --version 1.20.1 --values-file contour/contour-config.yaml

tanzu package install cert-manager --package-name cert-manager.community.tanzu.vmware.com --version 1.6.3

tanzu package install external-dns --package-name external-dns.community.tanzu.vmware.com --version 0.10.0 --values-file external-dns/external-dns-config.yaml

tanzu package install harbor --package-name harbor.community.tanzu.vmware.com --version 2.4.2 --values-file harbor/harbor-values.yaml

tanzu package install kpack --package-name kpack.community.tanzu.vmware.com --version 0.5.1 -f kpack/kpack-values.yaml

tanzu package install kpack-dependencies --package-name kpack-dependencies.community.tanzu.vmware.com --version 0.0.27 -f kpack/kpack-dependencies-values.yaml

tanzu package install cartographer --package-name cartographer.community.tanzu.vmware.com --version 0.3.0

tanzu package install fluxcd-kustomize-controller -p kustomize-controller.fluxcd.community.tanzu.vmware.com -v 0.21.1 --values-file fluxcd/kustomize-controller-values.yaml

tanzu package install fluxcd-source-controller --package-name fluxcd-source-controller.community.tanzu.vmware.com --version 0.21.5
```


