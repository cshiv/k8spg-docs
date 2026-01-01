## Troubleshooting Installation issues. 
TO-DO: Work in Progress , Needs comprehensive documentation

1. Check if RBAC permissions are correctly applied and check if required priviliges are present. User may run (./check_privilige.sh) for the permissions and fix issues if any.
2. Check the Deployment object or postgres object for any abnormalities.
   ```bash
   kubectl describe deploy/percona-postgresql-operator -n postgres-operator
   ```
3. Verify the Operator is running:
   ```bash
   kubectl get pods -n postgres-operator
   ```   
4. Check events for any information (By default events are stored in Kubernetes only for 60 minutes)
   ```bash
   kubectl get events --sort-by=".lastTimestamp" -n postgres-operator
   ```
5. Check the cluster Custom Resource:
   ```bash
   kubectl describe pg cluster1 -n postgres-operator
   ```

3. Review Operator logs for errors
```bash
kubectl logs deploy/percona-postgresql-operator -n postgres-operator
```