# K8S-LABS

## K8S LAB 2

#### 1-Create a ReplicaSet using the below yaml
```YAML

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: new-replica-set
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - command:
        - sh
        - -c
        - echo Hello Kubernetes! && sleep 3600
        image: busybox777
        imagePullPolicy: Always
        name: busybox-container
```
```bash
#Create a YAML file then apply it
vim deployment-1.yml
kubectl apply -f deployment-1.yml
```
![Screenshot from 2023-01-13 18-25-39](https://user-images.githubusercontent.com/103090890/212381631-0b6714c3-d46a-4394-89c3-ddc42bc3e258.png)

#### 2-How many PODs are DESIRED in the new-replica-set?

```bash
kubectl get replicaset.apps
```
![Screenshot from 2023-01-13 18-29-42](https://user-images.githubusercontent.com/103090890/212381892-e8d63706-65c7-405a-b7d9-fe7292e8068f.png)

#### 3-What is the image used to create the pods in the new-replica-set?
 the image is busybox777
```bash
kubectl describe replicaset.apps
```![Screenshot from 2023-01-13 18-32-54](https://user-images.githubusercontent.com/103090890/212383765-07ed5f2c-16a8-4885-ba6b-f532cad28d7b.png)



#### 4-How many PODs are READY in the new-replica-set?
```bash
kubectl get replicaset.apps
```
![Screenshot from 2023-01-13 18-34-36](https://user-images.githubusercontent.com/103090890/212384175-30e98537-1a2a-421c-a8e3-737f86f8111b.png)


#### 5-Why do you think the PODs are not ready?

I think the PODS are not ready due to an error in fetching the image from container registery which is DockerHub (no such image called busybox777)
```bash
kubectl describe replicaset.apps #OR
kubectl get pods
```
![Screenshot from 2023-01-13 18-42-31](https://user-images.githubusercontent.com/103090890/212384993-0c9dca76-af8f-436d-961c-c31b26fbf57d.png)
![Screenshot from 2023-01-13 18-43-16](https://user-images.githubusercontent.com/103090890/212385150-c0afec53-49d8-4de9-9449-3cbeee8feeac.png)


#### 6-Delete any one of the 4 PODs.
#### How many pods now
still 4 pods due to specified desired number of pods

```bash
kubectl delete po new-replica-set-69bsf
```
![Screenshot from 2023-01-13 18-44-08](https://user-images.githubusercontent.com/103090890/212385382-699a4896-8e88-4daf-9f50-cb6b9dd2889b.png)





#### 7-Why are there still 4 PODs, even after you deleted one?

Because of the controller on master node checks the worker node for the specified desired number of pods in the YAML file of deployment 
if there's any pod down for any reason it automatically creates another one



#### 8-Create a ReplicaSet using the below yaml

#### There is an issue with the file, so try to fix it.
```YAML
apiVersion: v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```
The issue in the YAML file is the apiVersion 
the apiVersion of Replica sets is apps/v1 

the right answer is 
```YAML 
apiVersion: apps/v1
```

Edited the file and applied it, now the Deployment is running
![Screenshot from 2023-01-13 18-49-58](https://user-images.githubusercontent.com/103090890/212386204-96c871e3-fb03-457e-ae0b-f393851cfc7d.png)

## Thank You !
