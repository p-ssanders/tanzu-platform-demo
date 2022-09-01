1.  Create a Harbor user per team
    1.  Username: name of dev team (e.g.: `dev-team`)
    1.  Password: Keepitsimple1
1.  Add the Harbor user as a member of the `cartographer` project with maintainer role
1.  Create a namepsace for the new team
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: dev-team
    ```
1.  Create a git repository for the new teams' app
1.  Create a secret with an SSH key for the git repository
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: git-credentials
      namespace: dev-team
    type: Opaque
    data:
      identity: BASE64_PRIVATE_KEY
      known_hosts: BASE64_KNOWN_HOSTS
    ```
1.  Create a service account per git repository in the namespace.
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: service-account
      namespace: dev-team
    secrets:
    - name: harbor-credentials # supply chain will create this
    - name: git-credentials
    imagePullSecrets:
    - name: harbor-credentials
    ```
1.  Apply [role](role.yaml) & [role bindings](role-binding.yaml) to the service account so it can create resources in the dev team's namespace
1.  Apply a workload per app