## Step - 1 : Create EKS Management Host in AWS ##

1) Launch new Ubuntu VM using AWS Ec2 ( t2.micro )	  
2) Connect to machine and install kubectl using below commands  
```
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```
3) Install AWS CLI latest version using below commands 
```
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

4) Install eksctl using below commands
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```
## Step - 2 : Create IAM role & attach to EKS Management Host ##

1) Create New Role using IAM service ( Select Usecase - ec2 ) 	
2) Add below permissions for the role <br/>
	- Administrator - acces <br/>
		
3) Enter Role Name (eksroleec2) 
4) Attach created role to EKS Management Host (Select EC2 => Click on Security => Modify IAM Role => attach IAM role we have created) 

## Step - 3 : Create EKS Cluster using eksctl ## 
**Syntax:** 

eksctl create cluster --name cluster-name  \
--region region-name \
--node-type instance-type \
--nodes-min 2 \
--nodes-max 2 \ 
--zones <AZ-1>,<AZ-2>

## N. Virgina: <br/>
```
eksctl create cluster --name ashokit-cluster4 --region us-east-1 --node-type t2.medium  --zones us-east-1a,us-east-1b
```
## Mumbai: <br/>
```
eksctl create cluster --name ashokit-cluster4 --region ap-south-1 --node-type t2.medium  --zones ap-south-1a,ap-south-1b
```

## Note: Cluster creation will take 5 to 10 mins of time (we have to wait). After cluster created we can check nodes using below command.

```
 kubectl get nodes  
```

### Note: We should be able to see EKS cluster nodes here. ##

### We are done with our Setup ###
	
## Step - 4 : After your practise, delete Cluster and other resources we have used in AWS Cloud to avoid billing ##

```
eksctl delete cluster --name ashokit-cluster4 --region ap-south-1
```
 
[Documentation collected from ashokitschool](https://github.com/ashokitschool/DevOps-Documents/blob/main/05-EKS-Setup.md) </br>

[Youtube help video] (https://www.youtube.com/watch?v=is99tq4Zwsc)

# Adding my learning to the Document

##### Create a Pod (simple example)
1. create a file named nginx-pod.yaml :
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

2. Apply the following commmand:
```
kubectl apply -f nginx-pod.yaml
```
3.  Check the pod
```
kubectl get pods
```
##### Access in a Pod and expose the application to browse
1. Use kubectl exec to open a shell inside the container:
```
kubectl exec -it nginx-pod -- /bin/bash
```
or , or, if the container only has sh:
```
kubectl exec -it nginx-pod -- /bin/sh
```
This gives you an interactive shell inside the pod.

#### Expose the Pod to Access from Outside
1. If you want to reach the application running inside the pod (like nginx on port 80):
```
kubectl expose pod nginx-pod \
  --type=LoadBalancer \
  --name=nginx-service \
  --port=80 \
  --target-port=80
```
2. Then check the service:
```
kubectl get svc nginx-service
```
3. You’ll see an EXTERNAL-IP once AWS provisions a load balancer. Open that IP in your browser to access the app.
####  Port Forward (Quick Local Access) Later try cannot complete this
If you don’t want to create a service, you can forward a local port to the pod:
```
kubectl port-forward pod/nginx-pod 8080:80
```
### Scaling (Pods vs Deployment)
A Pod is not scalable by itself. You can only run one instance.

To scale, you need a Deployment or ReplicaSet.For this create a file named nginx-deployment.yaml:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
2. Apply it
```
kubectl apply -f nginx-deployment.yaml
```
Now you’ll have 3 nginx pods. The same nginx-service will load balance traffic across all of them.
Verify Load Balancing
```
kubectl get endpoints nginx-service
```

### Scale Pods Later
```
kubectl scale deployment nginx-deployment --replicas=5
```

###### Perfect — let’s walk through how to map a custom domain name (like nginx.example.com) to your AWS LoadBalancer so you can browse your nginx pod with a friendly URL instead of the long ELB DNS.

