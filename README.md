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
#output
#Chocolatey (choco.exe) is now ready.
#You may need to shut down and restart powershell and/or consoles first prior to using choco.
```
Right now with last version **v0.26.0** we have an issue - [Error with pre-create check. When Creating a new docker machine](https://github.com/docker/machine/issues/4452)
Just rollback to **v0.25.0** with _--allow-downgrade_
```powershell
choco install minikube --version 0.25.0
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
kubectl.exe version
minikube.exe version
#output
#Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.2", GitCommit:"5fa2db2bd46ac79e5e00a4e6ed24191080aa463b", GitTreeState:"clean", BuildDate:"2018-01-18T10:09:24Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"windows/amd64"}
#Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.0", GitCommit:"fc32d2f3698e36b93322a3465f63a14e9f0eaead", GitTreeState:"clean", BuildDate:"2018-04-10T12:46:31Z", GoVersion:"go1.9.4", Compiler:"gc", Platform:"linux/amd64"}
#minikube version: v0.25.0
```
Minikube supports multiple versions of Kubernetes. To check out the different versions supported try out the following command.
```powershell
minikube get-k8s-versions
```
#Configuring Hyper-V

Find existing network adapters by running the **Get-NetAdapter**. Make a note of the network adapter name that you want to use for the virtual switch. 
```powershell
 Get-NetAdapter 
 #output
 #Name                      InterfaceDescription                    ifIndex Status
 #----                      --------------------                    ------- ------
 #vEthernet (ExternalSwi... Hyper-V Virtual Ethernet Adapter #2          18 Up
 #vEthernet (Default Swi... Hyper-V Virtual Ethernet Adapter             20 Up
 #Ethernet                  Intel(R) Ethernet Connection I217-LM          4 Up
 #VirtualBox Host-Only N... VirtualBox Host-Only Ethernet Adapter         2 Up
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
 #output
 #Kubectl is now configured to use the cluster.
 #Loading cached images from config file.
```
For check it we will try to get active nodes adn pods.
```powershell
 kubectl get nodes
 #output
 #NAME       STATUS    ROLES     AGE       VERSION
 #minikube   Ready     <none>    6m        v1.10.0
 kubectl get pod --all-namespaces
 #output
 #NAMESPACE     NAME                          READY     STATUS    RESTARTS   AGE
 #kube-system   kube-addon-manager-minikube   1/1       Running   1          16m
```
You can stop **Minikube** by simple stop **command**
```powershell
 minikube stop
 #output
 #Stopping local Kubernetes cluster...
 #Machine stopped.
```

#Dashboard
From this moment we could see **Minikube Dashboard**, right now it's empty. But later here we will see all details about internal live of our **K8s**.

```powershell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
#output
#secret "kubernetes-dashboard-certs" created
#serviceaccount "kubernetes-dashboard" created
#role "kubernetes-dashboard-minimal" created
#rolebinding "kubernetes-dashboard-minimal" created
#deployment "kubernetes-dashboard" created
#service "kubernetes-dashboard" created
minikube addons enable dashboard
#output
#dashboard was successfully enabled

```
