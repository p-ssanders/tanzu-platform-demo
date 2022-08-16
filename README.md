#   Tanzu Community Edition Demo

This repository contains configuration for demonstrating [Tanzu Community Edition](https://tanzucommunityedition.io/).

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
-   Generates a [Knative Serving](https://knative.dev/docs/serving/) `Service` (as an alternative to a `Deployment`, `Service`, and an `Ingress`) whenever images are published to Harbor to run the application
-   Provisions Let's Encrypt certificates for applications on the cluster
-   Creates DNS entries for applications on the cluster
-   Routes traffic to applications on the cluster from the Internet
-   Optionally creates a PostgreSQL database and binds it to an application

Given this cluster behavior, an application developer should be able to produce a running application with ingress from the Internet, a DNS name, and a valid Let's Encrypt certificate, with an attached PostgreSQL database by:
1.  Creating `Secret`s to store their Git repository access key, Harbor credentials, and desired PostgreSQL password
1.  Creating a `Workload` definition that references the git repository where source code is pushed
1.  Pushing a commit to their git repository

