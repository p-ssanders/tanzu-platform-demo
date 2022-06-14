#   kustomize

This directory is watched by the flux kustomize controller based on a [Flux Kustomization](../fluxcd/kustomizer.yaml) definition. Upon commit, the flux kustomize controller will apply the kubernetes manifests in this directory to the cluster.

Currently, this directory is used to manage the cluster-scoped cartographer supply chains. Supply chains make good GitOps candidates because they require a lot of complicated configuration where reconciling state in your head is unrealistic.