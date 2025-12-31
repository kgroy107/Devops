## Running minikube to learn the kubernetes
```minikube
 minikube config set driver docker
```

🔹 Effect
- Updates Minikube’s persistent configuration so that every time you start Minikube, it will use the Docker driver.

- This means Minikube will create Kubernetes clusters as Docker containers on your machine, instead of spinning up a VM.

- It’s especially useful if you already have Docker installed and want a lightweight setup.

🔹 Verify

The configuration ca be checked :
```minikube
minikube config view
```
🔹 Start Minikube

After setting the driver, start your cluster:
```minikube
minikube start
```
##### Start Minikube dashboard
```minikube
minikube dashboard --url
```
🔹 What happens
Minikube launches the Kubernetes Dashboard as a pod in the kubernetes-dashboard namespace.

Instead of opening a browser window automatically, the --url flag tells Minikube to just output the access URL in your terminal.

You’ll see something like:
```html
http://127.0.0.1:XXXXX/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
👉 This is a quick way to visually inspect your cluster: pods, deployments, services, and namespaces.

#### Install kubectl to communicate with minikube following documentation as per platform

🔹 Temporary alias (only for the current PowerShell session)
```script
Set-Alias -Name k -Value kubectl
```
🔹 Get all namespaces

```script
k get namespaces
```
🔹 creating namespace by command

```script
k get namespaces
```
🔹 creating a pod with the command (check the docker image server first)
```script
kubectl create deployment hello-node --image=registry.k8s.io/echoserver:1.10
```
🔹 Checking the events
```script
k get events -n dev
```
🔹 Creates a Service object in the dev namespace. The service listens on port 8080 and forwards traffic to the pods’ container port.
```script
k expose  deployment hello-node --type=LoadBalancer --port=8080 -n dev
```
Verify the service

```script
kubectl get svc -n dev
```

##### Deploying and customizing a Kubernetes application 
1. creating pod with v1.yaml
   
```script
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeployment
  labels:
    app: mywebapp
    tier: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mywebapp
      tier: frontend
  template:
    metadata:
      labels:
        app: mywebapp
        tier: frontend
    spec: # Pod spec
      containers:
      - name: mycontainer
        image: devopsjourney1/mywebapp
        ports:
        - containerPort: 80
```

##### Creating Kubernetes deployment

1. creating pod with v2.yaml
   
```script
apiVersion: v1
kind: Service
metadata:
  name: mywebapp
  labels:
    app: mywebapp
spec:
  ports:
  - port: 80
    protocol: TCP
    name: flask
  selector:
    app: mywebapp
    tier: frontend
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeployment
  labels:
    app: mywebapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mywebapp
      tier: frontend
  template:
    metadata: 
      labels:
        app: mywebapp
        tier: frontend
    spec: # Pod spec
      containers:
      - name: mycontainer
        image: devopsjourney1/mywebapp
        ports:
        - containerPort: 80
```
Watch by command that a service and a deployment has been creatred. To communicate with service, execute the following:
```script
k get service
```
```script
minikube service mywebapp 
```
##### Applying ConfigMap and scaling Pods
 ```script
apiVersion: v1
kind: Service
metadata:
  name: mywebapp
  labels:
    app: mywebapp
spec:
  ports:
  - port: 80
    protocol: TCP
    name: flask
  selector:
    app: mywebapp
    tier: frontend
  type: LoadBalancer
---
kind: ConfigMap 
apiVersion: v1 
metadata:
  name: myconfigmapv1.1
data:
  BG_COLOR: '#12181b'
  FONT_COLOR: '#FFFFFF'
  CUSTOM_HEADER: 'DevOps Journey!'
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeployment
  labels:
    app: mywebapp
spec:
  replicas: 5
  selector:
    matchLabels:
      app: mywebapp
      tier: frontend
  template:
    metadata:
      labels:
        app: mywebapp
        tier: frontend
    spec: # Pod spec
      containers:
      - name: mycontainer
        image: devopsjourney1/mywebapp
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: myconfigmapv1.1
```
##### Applying Resources to be consumed

Courtesy:[Kubernetes Tutorial for Beginners [1 Hour Course]](https://www.youtube.com/watch?v=1Lu1F94exhU))
