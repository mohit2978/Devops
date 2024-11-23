# Lecture 5

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

---
=> As of now, we have created POD directly using POD Manifest YML.
    
(kind: Pod)

=> If we create POD directly then we don't get self-healing capability.

=> If POD is damaged/crashed/deleted then k8s will not create new POD.

=> If pod damaged then our application will be down.

Note: We shouldn't create POD directly to deploy our application in k8s.

Note: We need to use k8s resources to create pods.

=> If we create pod using k8s resources, then pod life cycle will be managed by k8s.

=> We have below resources to create pods

      1) ReplicationController (Outdated)
      2) ReplicaSet
      3) Deployment
      4) DeamonSet
      5) StatefulSet
