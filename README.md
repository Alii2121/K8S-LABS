# K8S-LABS


<a name="K8S LAB 2"> <a/>
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
```
![Screenshot from 2023-01-13 18-32-54](https://user-images.githubusercontent.com/103090890/212383765-07ed5f2c-16a8-4885-ba6b-f532cad28d7b.png)



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


# K8S LAB 3


#### 1-Create a deployment called my-first-deployment of image nginx:alpine in the default namespace.
#### Check to make sure the deployment is healthy.

```bash
kubectl create deployment my-first-deployment --image=nginx:alpine --dry-run=client -oyaml > dep1.yml
kubectl apply -f dep1.yml
```



![Screenshot from 2023-01-14 16-38-31](https://user-images.githubusercontent.com/103090890/212477365-27f2e93d-94bb-44c9-9fa9-4270071232a1.png)







#### 2-Scale my-first-deployment up to run 3 replicas. Check to make sure all 3 replicas are ready.

```bash

kubectl scale deployment my-first-deployment --replicas=3
#OR best practice is modify the Yaml file itself to make replicas 3

```

![Screenshot from 2023-01-14 13-13-47](https://user-images.githubusercontent.com/103090890/212490918-cbb8ba7e-f7be-4728-9b6e-32a7bd982072.png)




#### 3-Scale my-first-deployment down to run 2 replicas.



```bash

kubectl scale deployment my-first-deployment --replicas=2
#OR best practice is modify the Yaml file itself to make replicas 2

```

![Screenshot from 2023-01-14 13-14-34](https://user-images.githubusercontent.com/103090890/212490964-72682963-a775-47c5-b3d1-09a5261bb47e.png)




#### 4-Change the image my-first-deployment runs from nginx:alpine to httpd:alpine .
```bash 
vim dep1.yml 
#change the image
kubectl apply -f dep1.yml
```
![Screenshot from 2023-01-14 13-17-19](https://user-images.githubusercontent.com/103090890/212491022-f1738fbe-c4bb-4c83-8736-ccf43749c209.png)


![Screenshot from 2023-01-14 13-17-05](https://user-images.githubusercontent.com/103090890/212491038-ad407b00-5d28-4f0b-8039-2b5a7f339a9d.png)





#### 5-Delete the deployment my-first-deployment


```bash 
kubectl delete deployment my-first-deployment  
```

![Screenshot from 2023-01-14 13-20-27](https://user-images.githubusercontent.com/103090890/212491419-f9a63511-9e60-4c59-bcfb-8b4e37d46f05.png)





#### 6-Create deployment from the below yaml
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
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
        image: busybox888
        imagePullPolicy: Always
        name: busybox-container
```


```bash
vim dep2.yml
kubectl apply -f dep2.yml
```
![Screenshot from 2023-01-14 13-29-00](https://user-images.githubusercontent.com/103090890/212491487-d3b58e33-fd2e-4a5c-86a0-f40adf0f9a7f.png)



#### 7-How many ReplicaSets exist on the system now?
```bash
kubectl get deployment
```

6 total replica sets

![Screenshot from 2023-01-14 13-32-14](https://user-images.githubusercontent.com/103090890/212491530-f9be3798-cce5-401e-aa3f-091f76c705b7.png)





#### 8-How many PODs exist on the system now?

```bash 
kubectl get pods 
```
6 pods 

#### 9-Out of all the existing PODs, how many are ready?


2 pods Are ready 











#### 10-What is the image used to create the pods in the new deployment?

```bash 
kubectl describe deployment
```
busybox888 image
![Screenshot from 2023-01-14 13-34-38](https://user-images.githubusercontent.com/103090890/212491645-fcb7a235-82eb-47bb-8232-1b5c9911c69c.png)



#### 11-Why do you think the deployment is not ready?
Error pulling the image from docker hub, No such image.







#### 12-Create a new Deployment using the below yaml 

No YAML file was provided


#### 13- There is an issue with the file, so try to fix it and correct the value of kind.

The issue is there is no Yaml file included :'D








## LAB 4

#### 1-How many Services exist on the system?


```bash 
kubectl get svc
```

![Screenshot from 2023-01-14 13-57-37](https://user-images.githubusercontent.com/103090890/212492171-f9659a26-a395-48ea-85f6-a2f4c12b913b.png)






#### 2-What is the type of the default kubernetes service?

The Default type is ClusterIP

![Screenshot from 2023-01-14 13-57-37](https://user-images.githubusercontent.com/103090890/212492205-896c808f-0a9a-436f-a7df-b039b2c05376.png)



#### 3-What is the targetPort configured on the kubernetes service?
 ```bash 
 kubectl describe svc
 ```
 target port is 6443/TCP

![Screenshot from 2023-01-14 13-59-07](https://user-images.githubusercontent.com/103090890/212492251-d536bb09-b230-4eb1-9e7b-da27cd5d57b4.png)




### 4-How many labels are configured on the kubernetes service?
```YAML
Labels:            component=apiserver
                   provider=kubernetes
```


![Screenshot from 2023-01-14 13-59-07](https://user-images.githubusercontent.com/103090890/212492300-816d23c8-3992-4c24-8c28-5322f2fcbec7.png)




#### 5-How many Endpoints are attached on the kubernetes service?
1 Endpoint = 172.30.1.2:6443


#### 6-Create a Deployment using the below yaml
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-webapp-deployment
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: simple-webapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: simple-webapp
    spec:
      containers:
      - image: kodekloud/simple-webapp:red
        imagePullPolicy: IfNotPresent
        name: simple-webapp
        ports:
        - containerPort: 8080
          protocol: TCP
          
 ```
 ```bash
 vim deployment.yml
 kubectl apply -f deployment.yml
 ```
 
 
 
 

#### 7-What is the image used to create the pods in the deployment?
```bash
kubectl describe deployment
```
kodekloud/simple-webapp:red



![Screenshot from 2023-01-14 14-04-38](https://user-images.githubusercontent.com/103090890/212492373-8b05ad18-57e5-4fbb-aa0b-ab607985a810.png)





#### 8-Create a new service to access the web application using the the below 
```YAML
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
```
The syntax of this Yaml file is wrong and it's missing key components 

The updated version :


![Screenshot from 2023-01-14 14-15-56](https://user-images.githubusercontent.com/103090890/212492403-d9110fd6-a2f2-4c4d-b1f3-9d195181bac1.png)




```bash
vim scv.yml
kubectl apply -f scv.yml
```




## Thank You !
