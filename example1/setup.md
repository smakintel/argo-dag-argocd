# Submit the DAG to argo

`argo submit -n argo --watch example1/dag-workflow.yaml`

go to argo dashboard and see 
`kubectl -n argo port-forward deployment/argo-server`

https://127.0.0.1:2746