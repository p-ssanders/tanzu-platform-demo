#   Tanzu Community Edition Demo

This repository contains configuration for demonstrating Tanzu Community Edition.

I used this configuration to deploy a TCE management cluster and one TCE workload cluster.

There is [configuration](tce/README.md) for installing the following TCE packages:
-   [contour](https://projectcontour.io/)
-   [cert-manager](https://cert-manager.io/)
-   [external-dns](https://github.com/kubernetes-sigs/external-dns/blob/master/README.md)
-   [harbor](https://goharbor.io/)
-   [kpack](https://github.com/pivotal/kpack)
-   [cartographer](https://cartographer.sh/)
-   [fluxcd](https://fluxcd.io/)
    -   [source-controller](https://github.com/fluxcd/source-controller/)
    -   [kustomize-controller](https://github.com/fluxcd/kustomize-controller/)
    -   [helm-controller](https://github.com/fluxcd/helm-controller/)

The packages have been configured such that the cluster automatically:
-   Builds application source code into images and publishes those images to Harbor whenever source code commits are pushed to a git repository
-   Generates a `Deployment`, `Service`, and an `Ingress` whenever images are published to Harbor
-   Provisions Let's Encrypt certificates for applications on the cluster
-   Creates DNS entries for applications on the cluster
-   Routes traffic to applications on the cluster from the Internet

Given this cluster behavior, an application developer should be able to produce a running application with ingress from the Internet, a DNS name, and a valid Let's Encrypt certificate by:
1.  Creating `Secret`s and a `ServiceAccount` to store their Harbor credentials and GitHub repository access key
1.  Creating a `Workload` definition that references the git repository where source code is pushed
1.  Pushing a commit to their git repository

Review [websocket-demo](apps/websocket-demo) for an example.

##  TODO
*   Implement a solution for application services like databases
*   Implement a solution for secure secrets
*   Implement a solution for cluster backups

##  References:
*   [Slides](https://docs.google.com/presentation/d/1CFA5tyyMMOR-1Zdu4b9tyDhvXkLY7e8Cd6W9W50f2uE)
*   [Cluster Evolution Diagrams](https://miro.com/app/board/uXjVO7K5t8c=/)
*   [TCE Install Guide](https://tanzucommunityedition.io/docs/v0.11/aws-intro/)
*   [Cluster Design Inspiration](https://tanzucommunityedition.io/docs/v0.11/solutions-secure-ingress/)