# k8s-live-talks
Simple K8s Demo project

#Installing.

You could download and install **minikube** from [Minikube Releases](https://github.com/kubernetes/minikube/releases) or by command at **PowerShell**.

Run **PowerShell** as Administrator.

At **PowerShell** enter we will enter all code.

Now let's install **choco**.
You can find updated information about installation at [Chocolatey](https://chocolatey.org/install)
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
Right now with last version **v0.26.0** we have an issue - [Error with pre-create check. When Creating a new docker machine](https://github.com/docker/machine/issues/4452)
Just rollback to **v0.25.0**
```powershell
choco install minikube --version 0.25.0 --allow-downgrade
#output
#ShimGen has successfully created a shim for minikube.exe
#The install of minikube was successful.
#Software install location not explicitly set, could be in package or default install location if installer.
#Chocolatey installed 2/2 packages.
#See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```
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
**_If you tun virtualbox keyword command you will get Oracle VM Virtualbox program opened._**
Minikube supports multiple versions of Kubernetes. To check out the different versions supported try out the following command.
```powershell
minikube get-k8s-versions
```
#Configuring Hyper-V

Find existing network adapters by running the **Get-NetAdapter**. Make a note of the network adapter name that you want to use for the virtual switch. 
```powershell
Get-NetAdapter 
```
Hyper-V is built into Windows as an optional feature - there is no Hyper-V download or installable component. There are several ways to enable the built-in Hyper-V role.
We will use PowerShell for it.
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
#output:
#Path          :
#Online        : True
#RestartNeeded : False
```
Create a virtual switch by using the **New-VMSwitch**. For example, to create an external virtual switch named **ExternalSwitch**, using the ethernet network adapter, and with **Allow management operating system to share this network adapter** turned on, run the following command
```powershell
New-VMSwitch -name ExternalSwitch  -NetAdapterName Ethernet -AllowManagementOS $true  
```
To create an internal switch, run the following command. 
```powershell
New-VMSwitch -name InternalSwitch -SwitchType Internal  
```
To create an private switch, run the following command. 
```powershell
New-VMSwitch -name PrivateSwitch -SwitchType Private 
```
#Starting our Cluster

We are now ready to launch our Kubernetes cluster locally. We will use the start command for it.  
```powershell
minikube start --vm-driver=hyperv --kubernetes-version="v1.10.0" --hyperv-virtual-switch "ExternalSwitch" --alsologtostderr
```

