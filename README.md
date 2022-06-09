#   Tanzu Community Edition Demo

This repository contains configuration for demonstrating Tanzu Community Edition.

I used this configuration to deploy a TCE management cluster and one TCE workload cluster.

There is configuration for installing the following TCE packages:
- [contour](https://projectcontour.io/)
- [cert-manager](https://cert-manager.io/)
- [external-dns](https://github.com/kubernetes-sigs/external-dns/blob/master/README.md)
- [harbor](https://goharbor.io/)
- [kpack](https://github.com/pivotal/kpack)
- [cartographer](https://cartographer.sh/)

The packages have been configured such that the cluster automatically:
-   Builds application source code into images and publishes those images to Harbor whenever source code commits are pushed to a git repository
-   Generates `Deployment`s whenever images are published to Harbor
-   Provisions Let's Encrypt certificates for applications on the cluster based on native `Ingress` definitions
-   Creates DNS entries for applications on the cluster based on native `Ingress` definitions
-   Routes traffic to applications on the cluster from the Internet based on native `Ingress` definitions

Given this cluster behavior, an application developer should be able to produce a running application with ingress from the Internet, a DNS name, and a valid Let's Encrypt certificate by:
1.  Creating a `Secret` and `ServiceAccount` to store their Harbor credentials
1.  Creating an `Image` definition that references the git repository where source code is pushed
1.  Creating a `Workload` definition that references the docker repository where images are pushed
1.  Pushing a commit to their git repository

##  TODO
*   Update `Image` to pull from a private git repo
*   Update `ClusterTemplate` to create a `Service` and an `Ingress` in addition to a `Deployment`

##  References:
*   [Slides](https://docs.google.com/presentation/d/1CFA5tyyMMOR-1Zdu4b9tyDhvXkLY7e8Cd6W9W50f2uE)
*   [Cluster Evolution Diagrams](https://miro.com/app/board/uXjVO7K5t8c=/)
*   [TCE Install Guide](https://tanzucommunityedition.io/docs/v0.11/aws-intro/)
*   [Cluster Design Inspiration](https://tanzucommunityedition.io/docs/v0.11/solutions-secure-ingress/)