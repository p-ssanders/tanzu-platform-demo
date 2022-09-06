#   Onboard a New Dev Team

This onboarding guide assumes the cluster will implement multi-tenancy through namespaces.

The name of the team must be kept consistent, and updated in the examples below.

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
1.  Create a service account per git repository in the namespace
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
1.  Apply role & role to the service account so it can create resources in the dev team's namespace
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: service-account-role
      namespace: dev-team
    rules:
    - apiGroups:
      - ""
      resources:
      - '*'
      verbs:
      - '*'
    - apiGroups:
      - serving.knative.dev
      resources:
      - '*'
      verbs:
      - '*'
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: service-account-role-binding
      namespace: dev-team
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: service-account-role
    subjects:
    - kind: ServiceAccount
      name: service-account
      namespace: dev-team
    ```
1.  Provide the developers with the `kubeconfig` to access the cluster.
1.  Apply a workload per app
