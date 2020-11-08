Docker registry using local registry
====================================

this location is available - (created) c:/Users/kmanimarpan/local-docker-registry

docker run -d  -p 5000:5000 --restart=always --name registry -v c:/Users/kmanimarpan/local-docker-registry:/var/lib/registry registry:2



# mounting path
minikube mount ${HOME}/host-path:/host


starting minikube 
==================
`minikube start --vm-driver=hyperv --insecure-registry="localhost:5000" --memory=8096 --cpus=2 --hyperv-virtual-switch="minikube" --v=7 --alsologtostderr`

If you see below error when starting minikube

W1108 09:59:46.114981    2864 out.go:146] �  Failed to start hyperv VM. Running "minikube delete" may fix it: recreate: creating host: create: creating: exit status 1
�  Failed to start hyperv VM. Running "minikube delete" may fix it: recreate: creating host: create: creating: exit status 1
I1108 09:59:46.118986    2864 out.go:110]

W1108 09:59:46.119981    2864 out.go:146] ❌  Exiting due to GUEST_PROVISION: Failed to start host: recreate: creating host: create: creating: exit status 1


make sure to run minikube start command using CMD as administrator.

`minikube dashboard`

# getting the minikube host ip
minikube ip 

# port forward local service running on laptop (e.g docker registry to minikube) - on minikube also i use same port 5000
ssh -i $(minikube ssh-key) docker@$(minikube ip) -R 5000:localhost:5000

# enablign local docker registry access to minikube (172.17.104.17= desktop machine localhost IP)
# below tunnel has to be open always

ssh -i $(minikube ssh-key) docker@$(minikube ip) -R 172.17.104.17:5000:registry.smakintel.local:5000


## to list images
curl -k -u admin:123next -X GET https://registry.smakintel.local:5000/v2/_catalog
curl -v https://registry.smakintel.local:5000/v2 -k -IL

curl -k -u admin:123next -X GET https://172.17.104.17:5000/v2/_catalog


# enablign local docker registry access to minikube (172.17.104.17= desktop machine localhost IP)
# Ref - https://medium.com/tarkalabs/proxying-services-into-minikube-8355db0065fd

ssh -i $(minikube ssh-key) docker@$(minikube ip) -R 172.17.104.17:5000:registry.smakintel.local:5000

# copy  local registry certs to minikube machine
scp -i $(minikube ssh-key) registry.smakintel.local.crt  docker@$(minikube ip):/home/docker
sudo mkdir -p /etc/docker/certs.d/registry.smakintel.local
sudo mv /home/docker/registry.smakintel.local.crt /etc/docker/certs.d/registry.smakintel.local

## login to registry
docker login registry.smakintel.local:5000
user name: admin
password: 123next


# tagging image with local registry
docker tag db:9.5 registry.smakintel.local:5000/db:9.5
docker push registry.smakintel.local:5000/db:9.5

# local docker registry path is C:\Users\kmanimarpan\local-docker-registry

list uploaded image
curl -k -u admin:123next -X GET https://registry.smakintel.local:5000/v2/_catalog

# A Secret is an object that stores a piece of sensitive data like a password or key. 
# Each item in a secret must be base64 encoded. Let’s create a secret for admin use. Encode the password (in our case — admin):
$ echo -n 'admin' | base64

# to decode
$ echo 'hashedTEXT' | base64 --decode


# pulling form private registry
## create k8s secret for docker registry
kubectl create secret docker-registry regcred --docker-server=registry.smakintel.local:5000 --docker-username=admin --docker-password=123next --docker-email=kavashgar.manimarpan@smakintel.com

in deployment file under container section
   imagePullSecrets:
      - name: regcred


# to get secrets
PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode)


# TO ACCESS POSTGRES DB form outside
you have to use proxy

$ kubectl proxy --port=15432

Now, you can navigate through the Kubernetes API to access this service using this scheme:

http://localhost:8080/api/v1/proxy/namespaces/<NAMESPACE>/services/<SERVICE-NAME>:<PORT-NAME>/

example to access db from localhost

http://localhost:5432/api/v1/proxy/namespaces/default/services/internal-db-service:15432/

# methods to access posgresql from local host outside clsuetr

kubectl port-forward postgresdb-7d86ffd484-b627w 15432:5432
                     (pod name)   (localhost port to be exposed : port from container )



# to enable minikube to access local docker register
ssh -i $(minikube ssh-key) docker@$(minikube ip) -R 172.17.104.17:5000:registry.nextlabs.
local:5000

# add registry.smakintel.local to /etc/hosts map to localhost
then do 

docker login registry.smakintel.local:5000
user name: admin
password: 123next