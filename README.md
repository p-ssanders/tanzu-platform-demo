#   Tanzu Community Edition Demo

This repository contains configuration for demonstrating [Tanzu Community Edition](https://tanzucommunityedition.io/) by using it to build a platform.

The platform is built using the following TCE packages:
-   [contour](https://projectcontour.io/) as a reverse proxy for ingress
-   [cert-manager](https://cert-manager.io/) for automated certificate management
-   [external-dns](https://github.com/kubernetes-sigs/external-dns/blob/master/README.md) for automated DNS record management
-   [harbor](https://goharbor.io/) to store and scan images
-   [kpack](https://github.com/pivotal/kpack) to build source code into images
-   [cartographer](https://cartographer.sh/) to coordinate the steps to turn source code into running application
-   [fluxcd](https://fluxcd.io/)
    -   [source-controller](https://github.com/fluxcd/source-controller/) for app-related GitOps
    -   [kustomize-controller](https://github.com/fluxcd/kustomize-controller/) for platform-related GitOps
    -   [helm-controller](https://github.com/fluxcd/helm-controller/) for Helm-based application deployments (e.g.: PostgreSQL)

The packages have been configured such that the platform (cluster) will automatically:
-   Build application source code into an image when commits are pushed to the application's git repository
-   Publish the image to Harbor
-   Deploy a [Knative Serving](https://knative.dev/docs/serving/) `Service` to run the application image
-   Deploy a PostgreSQL database and bind it to the application
-   Create DNS entries for the application based on application name
-   Route traffic from the Internet to the application using the `Host` header allowing for a single point of ingress into the cluster for any number of applications with different DNS names
-   Provision a Let's Encrypt certificate for the application's route

Given this behavior, an application developer is able to produce a running application with an attached PostgreSQL database, a DNS name, ingress from the Internet, and a valid Let's Encrypt certificate by simply:
1.  Creating a `Secret` to store their application's git repository access key
1.  Creating a `Workload` definition that references the git repository where source code is pushed
1.  Pushing a commit to their git repository to trigger the "source-to-url-with-postgres" supply chain

##  Example Workload

```yaml
---
apiVersion: carto.run/v1alpha1
kind: Workload
metadata:
  name: webapp-demo
  namespace: dev-team
  labels:
    apps.tanzu.vmware.com/workload-type: web-with-postgres
spec:
  params:
    - name: git_secret
      value: git-credentials
  source:
    git:
      ref:
        branch: main
      url: ssh://git@github.com:22/p-ssanders/tce-demo-webapp.git
  build:
    env:
      - name: BP_JVM_VERSION
        value: "18"
  env:
  - name: SPRING_PROFILES_ACTIVE
    value: cloud
```

##  Onboard a Development Team

See [Onboarding](tce/onboarding/README.md) for details.

##  Conventions

This platform has conventions built into it a team must consider, and assumptions it makes about a team's application for that application to succeed in the supply chains. See [Conventions](CONVENTIONS.md) for details.