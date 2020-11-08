# Install Argo Workflows

To get started quickly, you can use the quick start manifest which will install Argo Workflow as well as some commonly used components:

`kubectl create ns argo`
`kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/quick-start-postgres.yaml`

# Port forward the argo server to access GUI

`kubectl get svc -n argo`

`kubectl -n argo port-forward deployment/argo-server 2746:2746`

This will serve the user interface on http://localhost:2746


# Next, Download the latest Argo CLI from our releases page.
Extract and rename to argo.exe (solution in below)
https://github.com/argoproj/argo/issues/2683

place in windows C:\

type `argo verion` to get version


# Finally, submit an example workflow:


argo submit -n argo --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
argo list -n argo
argo get -n argo @latest
argo logs -n argo @latest
