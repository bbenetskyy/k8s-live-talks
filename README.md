# k8s-live-talks
Simple K8s Demo project

#Installing.

Download and install **minikube** from 

<https://github.com/kubernetes/minikube/releases>


Run **PowerShell** as Administrator.

At **PowerShell** enter we will enter all code.


Now let's install **choco**.
You can find updated information about installation at <https://chocolatey.org/install>
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
Installing **virtualbox**
```powershell
choco install virtualbox
```
Installing **kubectl**.
```powershell
Install-Script -Name install-kubectl -Scope CurrentUser -Force 
```
Try out that all if going in right way.
```powershell
cd \
VBoxManage -v
kubectl.exe version
minikube.exe version
```
**_If you tun virtualbox keyword command you will get Oracle VM Virtualbox program opened._**
Minikube supports multiple versions of Kubernetes. To check out the different versions supported try out the following command.
```powershell
minikube get-k8s-versions
```
#Starting our Cluster

We are now ready to launch our Kubernetes cluster locally. We will use the start command for it.  
```powershell
minikube.exe start --kubernetes-version="v1.10.0" --vm-driver="virtualbox" --alsologtostderr
```

**_If you have enabled Hyper-V you will get an exception:_**
```
This computer is running Hyper-V. VirtualBox won't boot a 64bits VM when Hyper-V is activated. Either use Hyper-V as a driver, or disable the Hyper-V hypervisor. (To skip this check, use --virtualbox-no-vtx-check)
```
So, as you can see in error description we could run it with Hyper-V engine:
```powershell
minikube.exe start --kubernetes-version="v1.10.0" --vm-driver="hyperv" --alsologtostderr
```
