# Lecture-6

>Note: remember we used ubuntu machine to create cluster so username is ubuntu only

### create cluster command

`
eksctl create cluster --name ashokit-cluster4 --region ap-south-1 --node-type t2.medium --zones ap-south-1a,ap-south-1b`

>Note: It takes time to start

### delete cluster command

`eksctl delete cluster --name ashokit-cluster4 --region ap-south-1
`


- to delete all the resources(pods,services all) we have created
        
        $ kubectl delete all --all

 - to get everything in any namespace

        $ kubectl get all  -n <namespace-name> 

- to execute manifest yml

        $ kubectl apply -f <yml-file>

- get all namespace

        $ kubectl get ns   


## K8S Deployment


=> It is one of the k8s resource/component

=> It is most recommended approach to deploy our applications in k8s.

=> Deployment will manage pod life cycle.

=> We have below advantages with K8s Deployment 

1) Zero Downtime

2) Auto Scaling

3) Rolling Update & Rollback


=> We have below deployment strategies

1) ReCreate (all at once)

2) RollingUpdate (one by one)


=> ReCreate means it will delete all existing pods and will create new pods.

=> RollingUpdate means it will delete and create new pod one by one.


```yml
---
apiVersion: apps/v1
kind: Deployment
metadata:
 name: javawebdeploy
spec:
 replicas: 2
 strategy: 
  type: RollingUpdate
 selector:
  matchLabels:
   app: javawebapp
 template:
  metadata:
   name: javawebpod
   labels:
    app: javawebapp
  spec:
   containers:
   - name: javawebappcontainer
     image: ashokit/javawebapp
     ports:
     - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
 name: javawebsvc
spec:
 type: LoadBalancer
 selector:
  app: javawebapp
 ports:
  - port: 80
    targetPort: 8080
...

```
>Note:quite understandable yml

$ kubectl delete all --all

$ kubectl get all

$ kubectl apply -f \<yml-file>

$ kubectl get all

Note: Access app using LBR URL

$ kubectl scale deployment javawebdeploy --replicas 4

$ kubectl get pods

$ kubectl scale deployment javawebdeploy --replicas 3



### Autoscaling

-> It is the process of increasing / decreasing infrastructure resources based on demand.

-> Autoscaling can be done in 2 ways

1) Horizontal Scaling

2) Verticle Scaling

-> Horizontal Scaling means increasing number of instances/servers/pods.

-> Veriticle Scaling means increasing capacity of single system.

    HPA : Horizontal POD Autoscaling

    VPA : Vertical POD Autoscaling (we don't use this)

HPA: It is used to scale up/down no.of pod replicas based on the observed metrics 
    (CPU or memory utilization)

-> HPA will interact with "Metric Server" to identify CPU/Memory utilization of POD.

-> Metrics server is an application that collect metrics from objects such as pods, nodes according to the state of CPU, RAM and keeps them in time.

- to get node metrics
        
        $ kubectl top nodes

- to get pod metrics
        
        $ kubectl top pods

>Note: By default metrics server is not available in our k8s cluster

