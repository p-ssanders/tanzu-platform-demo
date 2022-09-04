#   Conventions & Assumptions

The platform is a TCE management cluster and a TCE workload cluster where applications will run. The workload cluster can achieve multi-tenancy through namespaces, or it could just be a single-tenant cluster.

The platform is built using the following packages:

-   [contour](https://projectcontour.io/) as a reverse proxy for ingress into the cluster
-   [cert-manager](https://cert-manager.io/) for automated certificate management
-   [external-dns](https://github.com/kubernetes-sigs/external-dns/blob/master/README.md) for automated DNS record management
-   [harbor](https://goharbor.io/) to store and scan images
-   [kpack](https://github.com/pivotal/kpack) to build source code into images
-   [cartographer](https://cartographer.sh/) to coordinate the steps to turn source code into running application
-   [fluxcd](https://fluxcd.io/)
    -   [source-controller](https://github.com/fluxcd/source-controller/) for app-related GitOps
    -   [kustomize-controller](https://github.com/fluxcd/kustomize-controller/) for platform-related GitOps
    -   [helm-controller](https://github.com/fluxcd/helm-controller/) for Helm-based application deployments (e.g.: PostgreSQL)

The platform supports two supply chains, both of which assume a Spring Boot application:

-   `simple-deployment` -- Retrieve source code from a git repository, build it into an image, run the image on the cluster using kubernetes-native objects. Gets a URL, certificate, and ingress from the Internet.
-   `source-to-url-with-postgres` -- same as above, but uses Knative and deploys a PostgreSQL database and binds it to the application.

If a development team wants to deploy an application with a different stack or requirements such as a C#/.NET app with a Redis backend, they can, but there is no supply chain automation to do it. There is automation the team can leverage, such as kpack, cert-manager, external-dns, fluxcd, et al -- but none of it is automated in a supply chain.

The supply chains assume, expect, or produce the following:
-   The platform owners prepared a namespace for the development team by following the [Onboarding Guide](tce/onboarding/README.md). This includes setting up some resources on which the supply chains depend.
-   The application's git repository is private and accessed via SSH over the Internet
-   The source code in git is a Spring Boot application with a PostgreSQL driver on the classpath
-   There is a secret for git repository access using SSH authentication named `git-credentials` in the team's namespace.
    -   See FluxCD's [GitRepository](https://fluxcd.io/docs/components/source/gitrepositories/#ssh-authentication) spec to create the secret.
-   The PostgreSQL instance will have a single database named `{workload-name}`, with a user named `{workload-name}` and the password "keepitsimple"
-   The name of the image will be `{workload-name}/{namespace-name}`
-   The image will be stored in Harbor under the `cartographer` project
-   The URL will be https://workload-name.namespace-name.domain
    -   The domain is currently configured to be `development.samsanders.dev`