#   Debugging

Watch a workload get processed
```sh
tanzu apps workload tail webapp-demo -n dev-team --since 10m
```

Connect to a PostgreSQL instance
```sh
kubectl run postgresql-client --rm --tty -i --restart='Never' --namespace dev-team --image bitnami/postgresql:latest --env="PGPASSWORD=keepitsimple" --command -- psql --host postgresql-db -U webapp-demo -d webapp-demo -p 5432
```