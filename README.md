### Local K8S development using K3d

##### Install K8s related tools
```
1. kubectl  
2. kubectx + kubens
3. helm

sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens

Ref: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
Ref: https://github.com/ahmetb/kubectx
Ref: https://helm.sh/docs/intro/install/
```
##### Install K3d
```
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

Ref: https://k3d.io/v5.4.6/
```
##### Create/Delete cluster:
```
k3d cluster create mycluster
k3d cluster delete mycluster

OR

k3d cluster create mycluster -p "8080:80@loadbalancer" --agents 2
k3d cluster delete mycluster

Ref: https://k3d.io/v5.4.6/usage/exposing_services/
```

##### Create cluster with local conatner repository:
```
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
k3d registry create mycluster-registry --port 5000
k3d cluster create mycluster --registry-use mycluster-registry:5000

=>Add following entry in /etc/hosts file
127.0.0.1 k3d-mycluster-registry

=>Test:
docker pull nginx
docker tag nginx:latest k3d-mycluster-registry:5000/mynginx:v0.1
docker push k3d-mycluster-registry:5000/mynginx:v0.1
kubectl run mynginx --image k3d-mycluster-registry:5000/mynginx:v0.1
kubens
```

##### Deploy nginxdemo app with ingress:
```
Deploy:
kubectl apply -f nginxdemo.yaml

Test:
http://127.0.0.1:8080/
```
##### Output
![image](https://user-images.githubusercontent.com/23621486/211861480-e49395a2-65cb-4f5d-bb4b-61526979552c.png)

##### Create a local registry
```
$ k3d registry create myregistry.localhost --port 12345
INFO[0000] Creating node 'k3d-myregistry.localhost'     
INFO[0002] Pulling image 'docker.io/library/registry:2' 
INFO[0005] Successfully created registry 'k3d-myregistry.localhost' 
INFO[0005] Starting Node 'k3d-myregistry.localhost'     
INFO[0006] Successfully created registry 'k3d-myregistry.localhost' 
# You can now use the registry like this (example):
# 1. create a new cluster that uses this registry
k3d cluster create --registry-use k3d-myregistry.localhost:12345

# 2. tag an existing local image to be pushed to the registry  (OR can doenload image first docker pull nginx)
docker tag nginx:latest k3d-myregistry.localhost:12345/mynginx:v0.1

# 3. push that image to the registry
docker push k3d-myregistry.localhost:12345/mynginx:v0.1

# 4. run a pod that uses this image
kubectl run mynginx --image k3d-myregistry.localhost:12345/mynginx:v0.1

```

##### Install ArgoCD (Optional)
```
helm repo add argo https://argoproj.github.io/argo-helm
helm install my-argo-cd argo/argo-cd --version 5.26.0 --dry-run
helm pull argo/argo-cd --version 5.26.0 --untar --untardir argocd_chart
helm template --validate argo/argo-cd/

helm upgrade --install argocd ./argocd_chart/
watch -c kubectl get pod,svc,deploy

Ref: https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd
```
##### Important kubectl commands
```
kubectl get: Retrieve information about resources in a cluster.
kubectl describe: Show detailed information about a specific resource.
kubectl create: Create a new resource from a file or from stdin.
kubectl apply: Update an existing resource or create it if it doesn't exist.
kubectl delete: Delete a specific resource or a collection of resources.
kubectl logs: Print the logs for a container in a pod.
kubectl exec: Execute a command in a container.
kubectl scale: Scale the number of replicas of a resource.
kubectl rolling-update: Perform a rolling update of the pods in a deployment.
kubectl port-forward: Forward one or more local ports to a pod.
-------

kubectl get events
watch -c kubectl get pod,svc,deploy,ingress

kubectl get pod -o wide
kubectl describe pod <pod name>
kubectl logs -f <pod name> -c <container Name>
kubectl exec -it <pod name> -- /bin/bash

kubectl top pod --containers
kubectl top node
kubectl get nodes -o=jsonpath='{.items[*].metadata.name}'
kubectl get nodes -o=jsonpath='{.items[*].status.capacity.cpu}'
kubectl get nodes -o=jsonpath='{.items[*].status.nodeInfo.architecture}{.items[*].status.capacity.cpu}'
kubectl get nodes -o=jsonpath='{.items[*].status.nodeInfo.architecture}{"\n"}{.items[*].status.capacity.cpu}'
kubectl get nodes -o=custom-columns=NODE:.metadata.name,CPU:.status.capacity.cpu 
```
##### Important helm commands
```
helm install: Install a chart (a package of pre-configured Kubernetes resources) to a cluster.
helm list: List all of the releases (installed charts) in a cluster.
helm upgrade: Upgrade a release to a new version of a chart.
helm delete: Uninstall a release and delete the associated resources.
helm search: Search for charts in a chart repository.
helm repo: Add, list, remove, update, and index chart repositories.
helm show: Show the details of a chart.
helm template: Generate the resource manifest file from chart templates.
helm history: Show the revision history of a release.
helm rollback: Rollback a release to a previous revision.
----

helm upgrade --install argocd ./argocd_chart/

helm dependency update
helm template . --values values.yaml | less
helm template . --values values.yaml --show-only templates/deployment.yaml | less
```
