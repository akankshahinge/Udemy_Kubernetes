# Udemy_Kubernetes
##Pod
Pod is a basic entity in Kubernetes.
A pod can only one container in it and other containers can be helper containers.
```
$ kubectl run nginx --image=nginx
$ kubectl get pods
$ kubectl get pods -o wide
$ kubectl describe pod nginx
```
