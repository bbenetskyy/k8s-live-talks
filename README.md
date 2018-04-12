# k8s-live-talks
Simple K8s Demo project

#Installing.

Download and install **minikube** from 

<https://github.com/kubernetes/minikube/releases>


Run **PowerShell** as Administrator.

At **PowerShell** enter we will enter all code.

Installing **kubectl**.
```powershell
Install-Script -Name install-kubectl -Scope CurrentUser -Force 
```
Try out that all if going in right way.
```powershell
cd \
kubectl.exe version
minikube.exe version
```
Minikube supports multiple versions of Kubernetes. To check out the different versions supported try out the following command.

```powershell
minikube get-k8s-versions
```

#Starting our Cluster

We are now ready to launch our Kubernetes cluster locally. We will use the start command for it.  