# Udemy_Kubernetes
## Pod
Pod is a basic entity in Kubernetes.
A pod can only one container in it and other containers can be helper containers.
```
$ kubectl run nginx --image=nginx
$ kubectl get pods
$ kubectl get pods -o wide
$ kubectl describe pod nginx
```
YAML for pod
```
apiVersion: v1
kind: Pod
metadata: 
  name: myapp-pod
  labels:
    app: myapp
    type: frontend
spec:
  containers:
    - name: nginx-container
      image: nginx
      
>> kubectl create -f pod-definition.yml
```
Trick - To get dryrun of pod definition
```
$ kubectl run redis image=redis --dry-run=client -o yaml > pod.yaml
```
Trick - To edit pod definition
```
$ kubectl edit pod redis
```
