# k8s-local
Local K8S development using K3d

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
##### Deploy nginx app with ingress:
```
Deploy:
kubectl apply -f nginxdemo.yaml

Test:
http://127.0.0.1:8080/
```

