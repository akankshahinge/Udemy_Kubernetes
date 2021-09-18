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
$ kubectl replace -f pod.yaml (To get back the changes to yaml file)
```
Trick - To get the updated yaml file from etcd
```
$ kubectl get deployment nginx-deployment -o yaml > nginx-dep.yaml
```
Tip
```
We can have multiple definitions of object in one yaml file sepearted by "---"
```
## Replica Controller
Run multiple instances of a pod in kubernetes cluster for high availability. Replication controller always maintains a specified pods running. Another reason is for load balancing and scaling. If load increases on a single node then we expand load on multiple containers

#### Note: Replication controller is older technology and Replica set is a new way to set up the replication
YAML
```
apiVersion: apps/v1
kind: ReplicaSet 
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: frontend
spec:
  template:
    metadata: 
      name: myapp-pod
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx
    
  replicas: 6
  selector:
    matchLabels: 
      type: front-end
  
  >> kubectl create -f replicaset.yaml
  >> kubetcl get replicaset
  >> kubectl get pods
  >> kubectl delete replicaset myapp-replicaset

```
#### Note: The spec section contains definition of pod, so directly take from it. Selector is a necessary tag needed because replication controller can controller other pods as well. Selector tag is not present in replicationcontroller but it is present in replicationset.

#### Update Replicaset to increase replica count
1. Increase replica count
2. Run >> kubectl replace -f rs.yaml

Or directly use a kubernetes command to scale it
>> kubectl scale replicaset myapp-replicaset --replicas=2

Or 
>> kubectl edit replicaset myapp-replicaset

## Deployment
Deployment comes higher in hierarchy after replicaset. It is for rolling updates and upgrads.

YAML
To create a yaml file, take replicaset definition and just change kind to Deployment.
>> kubectl create -f deployment.yml
>> kubectl get deployments
>> kubectl get replicaset
>> kubectl get pods
>> kubectl get all   (Get all deployments,replicaset,pods)  

## Rollout
A new rollout creates a new deployment. If in future new updates is released then new deployment is craeted
```
>> kubectl rollout status deployment/myapp-dep
>> kubectl rollout history deployment/my-app
>> kubectl rollout undo deployment/myapp
```
### Deployment strategies
1. Recreate: In this strategy all containers are done down and then new containers are made up. But because of these there is application downtime
2. Rolling updates(Default deployment strategy): In this strategy the containers are taken down and new are brought up one by one while upgrading. So there is no downtime.

### Services
Services helps in communications. Communication between frontend and backend pods and frontend with outside world.
Types of Services:
1. NodePort: This service is accesssible to external user through a port. The service has port, The container port is TargetPort and outsite port is NodePort. NodePort is in the range of 30000-32767
YAML
```
apiVersion: v1
kind: Service
metadata: 
  name: myapp-service
specs:
  type: NodePort
  ports: 
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: myapp
    type: front-end
    
 >> kubectl create -f service.yaml
 >> kubectl get services
```
2. ClusterIP: This creates a network so that it communicated between frontend and backend services
YAML
```
apiVersion: v1
kind: Service
metadata:
 name: backend
specs:
 type: ClusterIP
 ports:
 - targerPort: 80
   port: 80
 selector:
   app: myapp
   type: backend

```
3. LoadBalancer: 

### Namespace
There are different namespaces craeted by kubernetes
1. Default: 
2. kube-system: It is used by kubernetes to create internal objects like DNS
3. kube-public: Here resources for all users are created

```
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
>> kubectl create -f namespace.yaml
Or
>> kubectl create namespace my-dev
>> kubectl get pods --namespace=mydev
>> kubectl get pods --all-namespaces
```
### Imperative vs Declarative
Imperative: In imperative way we declare the steps to achieve the goal. Eg. kubectl create, kubectl get, kubectl scale
Declarative: In declarative way we dont give steps but leave it itself to figure it out. Eg. kubectl apply
