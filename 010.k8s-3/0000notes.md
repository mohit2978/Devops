# Kubernetes Lecture 3

>Note: remember we used ubuntu machine to create cluster so username is ubuntu only

### create cluster command

`
eksctl create cluster --name ashokit-cluster4 --region ap-south-1 --node-type t2.medium --zones ap-south-1a,ap-south-1b`

>Note: It takes time to start

### delete cluster command

`eksctl delete cluster --name ashokit-cluster4 --region ap-south-1
`

![alt text](image.png)

no namespace so getting this!

## Manifest YML
=> In K8s we will use Manifest YML to deploy our application or to do anything in K8s cluster!!

=> Entire k8s is dependent on this yml,most people not able to write these only!!

### K8s Manifest YML Syntax

```yml
---
apiVersion: <version-number>
kind:  <resource-type>
metadata: <name>
spec: <container>
...
```
starts with 3 -(hyphens) and ends with 3 dots

apiVersion --> version of resource you are going to create

kind--> what type of k8s resource you want to create like if pod then kind is POD ,if service then SERVICE

metdata --> name to resource you want to give

spec-->here we specify docker image , here we tell about container!!


- Execute k8s manifest yml like below
    
        $ kubectl apply -f <manifest-yml>

we can create multiple resources from one yml,sections are separated by 3 hyphens

```yml
---
apiVersion: <version-number>
kind:  <resource-type>
metadata: <name>
spec: <container>
---
apiVersion: <version-number>
kind:  <resource-type>
metadata: <name>
spec: <container>
---
apiVersion: <version-number>
kind:  <resource-type>
metadata: <name>
spec: <container>
...
```

These yml file you can get from internet and chatgpt 

### Example K8S POD Manifest YML

```yml
---
apiVersion: v1
kind: Pod
metadata:
 name: javawebapppod
 labels:
  app: javawebapp
spec:
 containers:
 - name: javawebappcontainer
   image: ashokit/javawebapp
   ports:
   - containerPort: 8080
...
```
In metadata we give label with name!!






