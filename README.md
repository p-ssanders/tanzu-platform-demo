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

The packages have been configured such that the cluster will automatically:
-   Build application source code into images whenever source code commits are pushed to a git repository
-   Publishes images to Harbor
-   Generate a [Knative Serving](https://knative.dev/docs/serving/) `Service` to run the application image
-   Deploy a PostgreSQL database and bind it to the application
-   Provision a Let's Encrypt certificate for the application
-   Create DNS entries for the application based on application name
-   Route traffic from the Internet to the application using the `Host` header allowing for a single point of ingress into the cluster for any number of applications with different DNS names

Given this cluster behavior, an application developer is able to produce a running application with ingress from the Internet, a DNS name, and a valid Let's Encrypt certificate, with an attached PostgreSQL database by:
1.  Creating `Secret`s to store their Git repository access key and Harbor credentials
1.  Creating a `Workload` definition that references the git repository where source code is pushed
1.  Pushing a commit to their git repository to trigger the supply chain

