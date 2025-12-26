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
