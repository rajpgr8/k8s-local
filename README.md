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
##### Deploy nginxdemo app with ingress:
```
Deploy:
kubectl apply -f nginxdemo.yaml

Test:
http://127.0.0.1:8080/
```
##### Output
![image](https://user-images.githubusercontent.com/23621486/211861480-e49395a2-65cb-4f5d-bb4b-61526979552c.png)

##### Install ArgoCD (Optional)
```
$ helm upgrade --install argocd ./argocd_chart/
$ watch -c kubectl get pod,svc,deploy

Ref: https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd
```
##### Important kubectl commands
```
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
