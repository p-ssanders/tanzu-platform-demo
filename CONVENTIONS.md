#   Conventions

This cluster supports a single supply chain, `source-to-url-with-postgres`, that will retrive source code from a git repository, build it into an image, run the image on the cluster, and deploy a PostgreSQL database to which the application can/will bind. The source code must be a Spring Boot application.

There are no supported paths to a URL other than this one. For example, if a development team wants to deploy a Spring Cloud Gateway, or a C#/.NET app, they can, but there is no supply chain automation to do it. There is automation the team can leverage, such as kpack, cert-manager, external-dns, fluxcd, et al -- but none of it is automated in a supply chain.

This cluster and supply chain has some conventions built into it:
-   The supply chain itself is utilizing "GitOps." The supply chain configuration lives in the [kustomize/](kustomize/) directory which is being watched by a [FluxCD Kustomizer](fluxcd/kustomize/kustomizer.yaml) to automatically synchronize the state of the cluster with the state described in the `kustomize/` directory.
-   Assumption: The git repository is private and accessed via SSH
-   Assumption: the source code in git is a Spring Boot application with a PostgreSQL driver on the classpath
-   Assumpton: there is a secret for git repository access using SSH authentication named `git-credentials`.
    -   See FluxCD's [GitRepository](https://fluxcd.io/docs/components/source/gitrepositories/#ssh-authentication) spec to create the secret.
-   The operator team must prepare a namespace for the dev team by following the [Onboarding Guide](tce/onboarding/README.md). This includes setting up some resources on which the supply chain depends, but ideally developers should not need to maintain.
-   The PostgreSQL instance will have a single database named `{workload-name}`, with a user named `{workload-name}` and the password "keepitsimple"
-   The name of the image will be `{workload-name}/{namespace-name}`
-   The image will be stored in Harbor under the `cartographer` project
-   The URL will be https://workload-name.namespace-name.domain
    -   The domain is currently configured to be `development.samsanders.dev`