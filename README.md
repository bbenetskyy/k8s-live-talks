# k8s-live-talks
Simple K8s Demo project

_**IMAGE DEMO OF THEORY**_

# You Could install now it with Docker Edge!!!

![Enabling K8s at Docker Edge](https://github.com/bbenetskyy/k8s-live-talks/blob/master/enable_kubernate.png)

Next Installign and configuration of Hyper-V is not needed any more. After Install K8s via Docker Edge you could use it immedialy))))

# Installing.


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
Let's **search** at **choco** for all packages connected with **minikube**
```powershell
choco search kube
#output
#Chocolatey v0.10.10
#kubernetes-cli 1.10.1 [Approved]
#Minikube 0.26.0 [Approved]
#kubernetes-node 1.10.0 [Approved] Downloads cached for licensed users
#kubernetes-kompose 1.11.0 [Approved]
#juju 2.3.5 [Approved]
#kubernetes-helm 2.8.2 [Approved] Downloads cached for licensed users
#openshift-cli 3.9.0 [Approved]
#minishift 1.15.1 [Approved]
#kubeval 0.7.0 [Approved] Downloads cached for licensed users
#faas-cli 0.5.0 [Approved] Downloads cached for licensed users
#10 packages found.
```
From that list we need to install 
* Minikube
* kubernetes-cli*
* kubernetes-node
* kubernetes-kompos

*Please note that package **kubernetes-cli** will be installed with **Minikube**.

But we will start by installing **docker** as first package:
```powershell
choco install docker -y
choco install minikube -y
choco install kubernetes-node -y
choco install kubernetes-kompose -y
```
'**-y**' option means '**Yes**' and velocity out installation process. Question to ensure that user want to install that will be asked each time when we call **choco install**.
```powershell
Do you want to run the script?([Y]es/[N]o/[P]rint)
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
# Configuring Hyper-V

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
# Starting our Cluster

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
 kubectl cluster-info
 #output
 #Kubernetes master is running at https://10.10.34.139:8443
```
You can stop **Minikube** by simple stop **command**
```powershell
 minikube stop
 #output
 #Stopping local Kubernetes cluster...
 #Machine stopped.
```
If you get some errors connected with machine state, you can **delete** it.
```powershell
 minikube delete
 #output
 #Deleting local Kubernetes cluster...
 #Machine deleted.
```
# Dashboard
From this moment we could see **Minikube Dashboard**, right now it's empty. But later here we will see all details about internal live of our **K8s**.

```powershell
minikube addons enable dashboard
#output
#dashboard was successfully enabled
```

If you will open your browser and enter [10.10.33.235:30000](http://10.10.33.235:30000/#!/overview?namespace=default) you will find list of acceptable Api Requests.

_**IMAGE DEMO OF PODS IN K8S**_

# Run Simple First Pod

Here is simplest first pod definition in our live
```yml
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
spec:
  containers:
  - name: hello-ctr
    image: bbenetskyy/k8s-python-api:first
    ports:
    - containerPort: 80
```
Just save it for example as **[pod_first.yml](https://github.com/bbenetskyy/k8s-live-talks/blob/master/pod_first.yml)** file.
And start new pod from **PowerShell**
```powershell
kubectl create -f pod_first.yml
#output
#pod "hello-pod" created
```
Not we could check is it true that pod getting created
```powershell
kubectl get pods
#output
#NAME        READY     STATUS    RESTARTS   AGE
#hello-pod   1/1       Running   0          1h
```
You see all nodes in default namespace. You could specify namespace or an actual node
```powershell
kubectl get pods/hello-pod
#output
#NAME        READY     STATUS    RESTARTS   AGE
#hello-pod   1/1       Running   0          1h
```
Also if you need you could see all pods in a system
```powershell
kubectl get pods --all-namespaces
#output
#NAMESPACE     NAME                                    READY     STATUS    RESTARTS   AGE
#default       hello-pod                               1/1       Running   0          1h
#kube-system   kube-dns-86f4d74b45-bj8pb               3/3       Running   11         15d
#...
```
More detailed information about statuses and processing during creation you could see whe describe pods
```powershell
kubectl describe pods
#output
#Name:         hello-pod
#Namespace:    default
#Node:         minikube/10.10.33.55
#...
#Status:       Running
#IP:           172.17.0.4
#...
#Containers:
#  hello-ctr:
#    Container ID:   docker://8e47bc06734be1955843afb90c6ac209911abb5ff889e927151b83f130bea255
#    Image:          bbenetskyy/k8s-python-api:first
#...
```
 Where Status - show real **legal** pod status.

 Where Containers=>hello-ctr=>State - container state
 
 And Containers=>hello-ctr=>State=>Reason - reason  of container state,

 So the pod status we were getting was actually the **container state**, not the **pod state**.

_**IMAGE DEMO OF RC IN K8S**_

# Replication Controller

We won't work in real live with a Pods but with with higher level abstraction names **Replication Controller**.

Replication Controller, later RC only, creates a pod and could make his replication and in reconciliation loop.

K8s picks up the hard work, runs a watch loop in the background and makes sure that the actual state of the cluster always matches your desired state.

Before starting with RC, remove created pod
```powershell
kubectl delete pods hello-pod
#output
#pod "hello-pod" deleted
```
Now create new pod by using RC. Here you could see definition of **[rc_first.yml](https://github.com/bbenetskyy/k8s-live-talks/blob/master/rc_first.yml)**
```yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: hello-rc
spec:
  replicas: 10
  selector:
    app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-pod
        image: bbenetskyy/k8s-python-api:first
        ports:
        - containerPort: 80
```
Create RC from that file. And check if Pods and RC was created
```powershell
kubectl create -f rc_first.yml
#output
#replicationcontroller "hello-rc" created
kubectl get pod
#output
#NAME             READY     STATUS              RESTARTS   AGE
#hello-rc-2q7dn   0/1       ContainerCreating   0          8s
#hello-rc-58hnl   1/1       Running             0          8s
#hello-rc-blhdv   0/1       ContainerCreating   0          8s
#hello-rc-bwnwb   0/1       ContainerCreating   0          8s
#hello-rc-bx8md   0/1       ContainerCreating   0          8s
#hello-rc-k5z8d   1/1       Running             0          8s
#hello-rc-kttzs   0/1       ContainerCreating   0          8s
...
kubectl get rc
#output
#NAME       DESIRED   CURRENT   READY     AGE
#hello-rc   10        10        7         17s
kubectl describe rc
#output
#Name:         hello-rc
#Namespace:    default
#Selector:     app=hello-world
#...
# Type    Reason            Age   From                    Message
#  ----    ------            ----  ----                    -------
#  Normal  SuccessfulCreate  30s   replication-controller  Created pod: hello-rc-2q7dn
#  Normal  SuccessfulCreate  30s   replication-controller  Created pod: hello-rc-58hnl
#  Normal  SuccessfulCreate  30s   replication-controller  Created pod: hello-rc-z6vfn
#...
```
You can see that RC create and run for us 10 Pods instances with one container in each. Also at detailed description of rc we could check that it's monitoring described count of Pods.

Let's change **replicas** to 20 for example and apply our changes for running k8s RC.
```powershell
kubectl apply -f rc_first.yml
#output
#Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
#replicationcontroller "hello-rc" configured
kubectl get rc -o wide
#output
#NAME       DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES                                      SELECTOR
#hello-rc   20        20        16        7m        hello-pod    bbenetskyy/k8s-python-api:first   app=hello-world
```
_***-o wide** just provide Containers, Images and Selector columns to output_

We just add to 10 existed new 10 Pods by changing described property by running RC and K8s take all work for changes by himself.

_**IMAGE DEMO OF KS IN K8S**_

#K8s Services
 Services are needed to see our pods in out world. If you will try to get your pods by his IP address with exposed port(80 in our).

 You will get nothing!
 
 We need to start Kubernates Service, later just **KS**, to make our containers visible from Pods. During creating we will set service name **--name=hello-svc**, targeting port **--target-port=80** and service type **--type=NodePort**
```powershell
kubectl expose rc hello-rc --name=hello-svc --target-port=80 --type=NodePort
#output
#service "hello-svc" exposed
 kubectl describe svc hello-svc
#output
#Name:                     hello-svc
#Namespace:                default
#Labels:                   app=hello-world
#...
#Type:                     NodePort
#IP:                       10.101.166.184
#Port:                     <unset>  80/TCP
#TargetPort:               80/TCP
#NodePort:                 <unset>  31236/TCP
#...
```
**10.101.166.184** - service IP; same as minikube IP;

**31236/TCP** - port that we can access it on; Service ports are going to be between 30000 and 32767;

Now let's make KS correctly via YAML script. Firstly just kill existed KS instance
```powershell
kubectl delete svc hello-svc
#output
#service "hello-svc" deleted
```
And now create a **[svc.yml file](https://github.com/bbenetskyy/k8s-live-talks/blob/master/svc.yml)**.
```yml
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
  labels:
    app: hello-world
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: hello-world
```
Before run it let's focus on **ServiceType**. It could be
* **ClusterIP** - stable internal cluster IP, that is only make the service available to other nodes in the cluster.
* **NodePort** - exposes the app outside of the cluster by adding a cluster-wide TCP of UDP port on top of **ClusterIP**.
* **LoadBalancer** - integrates **NodePort** with cloud-based load balancers.

Now we know more about that we a doing so let's run KS configuration
```powershell
 kubectl create -f svc.yml
#output
#service "hello-svc" created
```
And basic checking that all going fine
```powershell
kubectl get svc
#output
#NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
#hello-svc    NodePort    10.97.177.177   <none>        80:31656/TCP   1m
#kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          17d
kubectl describe svc hello-svc
#output
#Name:                     hello-svc
#Namespace:                default
#Labels:                   app=hello-world
#Annotations:              <none>
#Selector:                 app=hello-world
#Type:                     NodePort
#IP:                       10.97.177.177
#Port:                     <unset>  80/TCP
#TargetPort:               80/TCP
#NodePort:                 <unset>  31656/TCP
#...
```
*_we could specify port by adding nodePort to YAML_

Now we could check that old KS port is closed but newly created is available for us)))

As summary for KS we should remember that after creating KS, K8s create endpoint and check if take care of all Pods by his labels. We could ensure about it by checking al KS listening addresses.
```powershell
kubectl describe ep hello-svc
#output
#Name:         hello-svc
#Namespace:    default
#Labels:       app=hello-world
#Annotations:  <none>
#Subsets:
#  Addresses:          172.17.0.10,172.17.0.11,172.17.0.12,172.17.0.13,172.17.0.14,172.17.0.15,172.17.0.16,172.17.0.17,172.17.0.18,172.17.0.19,172.17.0.20,172.17.0.21,172.17.0.22,172.17.0.23,172.17.0.3,172.17.0.5,172.17.0.6,172.17.0.7,172.17.0.8,172.17.0.9
#  NotReadyAddresses:  <none>
#  Ports:
#    Name     Port  Protocol
#    ----     ----  --------
#    <unset>  80  TCP

#Events:  <none>
```
_**IMAGE DEMO WITH LABELS**_

# Deploys

As usually before starting new Deploys with K8s Deployments we should kill worked RC. Want to mention that Deployment located on higher level than RC.

```powershell
kubectl delete rc hello-rc
#output
#replication controller "hello-rc" deleted
kubectl get pods
#output
#NAME             READY     STATUS        RESTARTS   AGE
#hello-rc-2q7dn   0/1       Terminating   3          5d
#hello-rc-4dl6v   1/1       Terminating   3          5d
#hello-rc-5s46p   1/1       Terminating   3          5d
#hello-rc-7dm5l   0/1       Terminating   3          5d
#hello-rc-blhdv   0/1       Terminating   3          5d
#hello-rc-bwnwb   0/1       Terminating   3          5d
#hello-rc-bx8md   1/1       Terminating   3          5d
#hello-rc-g7hf7   0/1       Terminating   3          5d
#hello-rc-gzjpc   0/1       Terminating   3          5d
#hello-rc-k5z8d   1/1       Terminating   3          5d
#hello-rc-kttzs   1/1       Terminating   3          5d
#hello-rc-sk9rq   1/1       Terminating   3          5d
#hello-rc-vqhwv   0/1       Terminating   3          5d
#hello-rc-wtbdb   0/1       Terminating   3          5d
#hello-rc-xq7x8   1/1       Terminating   3          5d
#hello-rc-z6vfn   1/1       Terminating   3          5d
#hello-rc-zbthp   0/1       Terminating   3          5d
```
Now we check that our KS still works:
```powershell
kubectl describe svc hello-svc
#output
#Name:                     hello-svc
#Namespace:                default
#Labels:                   app=hello-world
#Annotations:              <none>
#Selector:                 app=hello-world
#Type:                     NodePort
#IP:                       10.97.177.177
#Port:                     <unset>  80/TCP
#TargetPort:               80/TCP
#NodePort:                 <unset>  31656/TCP
#Endpoints:                <none>
#Session Affinity:         None
#External Traffic Policy:  Cluster
#Events:                   <none>
```

_**CHECK ALL API VERSIONS MOST IMPORTANT IS DEPLOYMENT**_

As in early we will define YAML Deployment declaration for **kubectl**.

Code for defining Deploy K8s we will locate at **[deploy.yml file](https://github.com/bbenetskyy/k8s-live-talks/blob/master/deploy.yml)**
```yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hello-deploy
spec:
  replicas: 10
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-pod
        image: bbenetskyy/k8s-python-api:first
        ports:
        - containerPort: 80
```
And create Deploy for K8s with **kubectl create** command:
```powershell
kubectl create -f deploy.yml
#output
#deployment.extensions "hello-deploy" created

kubectl describe deploy hello-deploy
#output
#Name:                   hello-deploy
#Namespace:              default
#CreationTimestamp:      Tue, 15 May 2018 12:50:55 +0200
#Labels:                 app=hello-world
#Annotations:            deployment.kubernetes.io/revision=1
#Selector:               app=hello-world
#Replicas:               10 desired | 10 updated | 10 total | 10 available | 0 unavailable
#StrategyType:           RollingUpdate
#MinReadySeconds:        0
#RollingUpdateStrategy:  1 max unavailable, 1 max surge
#Pod Template:
#  Labels:  app=hello-world
#  Containers:
#   hello-pod:
#    Image:        bbenetskyy/k8s-python-api:first
#    Port:         80/TCP
#    Host Port:    0/TCP
#    Environment:  <none>
#    Mounts:       <none>
#  Volumes:        <none>
#Conditions:
#  Type           Status  Reason
#  ----           ------  ------
#  Available      True    MinimumReplicasAvailable
#  Progressing    True    NewReplicaSetAvailable
#OldReplicaSets:  <none>
#NewReplicaSet:   hello-deploy-599bcfb9b9 (10/10 replicas created)
#Events:
#  Type    Reason             Age   From                   Message
#  ----    ------             ----  ----                   -------
#  Normal  ScalingReplicaSet  36s   deployment-controller  Scaled up replica set hello-deploy-599bcfb9b9 to 10

kubectl get rs
#output
#NAME                      DESIRED   CURRENT   READY     AGE
#hello-deploy-599bcfb9b9   10        10        10        1m

kubectl describe rs
#output
#Name:           hello-deploy-599bcfb9b9
#Namespace:      default
#Selector:       app=hello-world,pod-template-hash=1556796565
#Labels:         app=hello-world
#                pod-template-hash=1556796565
#Annotations:    deployment.kubernetes.io/desired-replicas=10
#                deployment.kubernetes.io/max-replicas=11
#                deployment.kubernetes.io/revision=1
#Controlled By:  Deployment/hello-deploy
#Replicas:       10 current / 10 desired
#Pods Status:    10 Running / 0 Waiting / 0 Succeeded / 0 Failed
#Pod Template:
#  Labels:  app=hello-world
#           pod-template-hash=1556796565
#  Containers:
#   hello-pod:
#    Image:        bbenetskyy/k8s-python-api:first
#    Port:         80/TCP
#    Host Port:    0/TCP
#    Environment:  <none>
#    Mounts:       <none>
#  Volumes:        <none>
#Events:
#  Type    Reason            Age   From                   Message
#  ----    ------            ----  ----                   -------
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-d4nb4
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-88vfh
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-rjw4x
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-d7zmx
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-n8nm9
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-pwgp8
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-k6mlt
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-psd87
#  Normal  SuccessfulCreate  1m    replicaset-controller  Created pod: hello-deploy-599bcfb9b9-c95fz
#  Normal  SuccessfulCreate  1m    replicaset-controller  (combined from similar events): Created pod: hello-deploy-599bcfb9b9-p6fbv

#  **minReadySeconds - how much wait when complete this and move to other**
```

  _**SEARCH ALL STRATEGY TYPES**_

_Cool we have it! What's next???_

In next step we will see no-downtime update with K8s Deploy
```powershell
kubectl apply -f deploy_two.yml --record
#output
#Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
#deployment.extensions "hello-deploy" configured
```

_Hmm, and what's change?_ 

Lest check our deployment - **hello-deploy**

```powershell
kubectl rollout status deployment hello-deploy
#output
#Waiting for rollout to finish: 2 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 2 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 2 out of 10 new replicas have been updated...
kubectl get deploy hello-deploy
#output
#NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
#hello-deploy   10        11        4            9           9m
```

Check it in your browser. You should see a new version of API

_**OUTPUT PAGE FROM BROWSER**_

_Ok, we get new version. It works. But what if I want to roll back to previous one, what should I do?_

You could simply roll back if you make deploy with flag **-f**, but if you don't use it K8s will not register you deployment changes.

To check update history run next:
```powershell
kubectl rollout history deployment hello-deploy
#output
#deployments "hello-deploy"
#REVISION  CHANGE-CAUSE
#1         <none>
#2         kubectl.exe apply --filename=deploy_two.yml --record=true
```

**One more time - _Is no record flat what revision 2 will be have none changes_**

**All time use _--record=true_ flag**

Some additional information about K8s inside before roll back
```powershell
kubectl get rs
#output
#NAME                      DESIRED   CURRENT   READY     AGE
#hello-deploy-599bcfb9b9   0         0         0         11m
#hello-deploy-bc445bff9    10        10        10        3m

kubectl describe deploy hello-deploy
#output
#Name:                   hello-deploy
#Namespace:              default
#CreationTimestamp:      Tue, 15 May 2018 12:50:55 +0200
#Labels:                 app=hello-world
#Annotations:            deployment.kubernetes.io/revision=2
#                        kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"extensions/v1beta1","kind":"Deployment","metadata":{"annotations":{"kubernetes.io/change-cause":"kubectl.exe apply --filename=deploy_two...
#                        kubernetes.io/change-cause=kubectl.exe apply --filename=deploy_two.yml --record=true
#Selector:               app=hello-world
#Replicas:               10 desired | 10 updated | 10 total | 10 available | 0 unavailable
#StrategyType:           RollingUpdate
#MinReadySeconds:        10
#RollingUpdateStrategy:  1 max unavailable, 1 max surge
#Pod Template:
#  Labels:  app=hello-world
#  Containers:
#   hello-pod:
#    Image:        bbenetskyy/k8s-python-api:second
#    Port:         80/TCP
#    Host Port:    0/TCP
#    Environment:  <none>
#    Mounts:       <none>
#  Volumes:        <none>
#Conditions:
#  Type           Status  Reason
#  ----           ------  ------
#  Available      True    MinimumReplicasAvailable
#  Progressing    True    NewReplicaSetAvailable
#OldReplicaSets:  <none>
#NewReplicaSet:   hello-deploy-bc445bff9 (10/10 replicas created)
#Events:
#  Type    Reason             Age              From                   Message
#  ----    ------             ----             ----                   -------
#  Normal  ScalingReplicaSet  11m              deployment-controller  Scaled up replica set hello-deploy-599bcfb9b9 to 10
#  Normal  ScalingReplicaSet  3m               deployment-controller  Scaled up replica set hello-deploy-bc445bff9 to 1
#  Normal  ScalingReplicaSet  3m               deployment-controller  Scaled down replica set hello-deploy-599bcfb9b9 to 9
#  Normal  ScalingReplicaSet  3m               deployment-controller  Scaled up replica set hello-deploy-bc445bff9 to 2
#  Normal  ScalingReplicaSet  2m               deployment-controller  Scaled down replica set hello-deploy-599bcfb9b9 to 7
#  Normal  ScalingReplicaSet  2m               deployment-controller  Scaled up replica set hello-deploy-bc445bff9 to 4
#  Normal  ScalingReplicaSet  2m               deployment-controller  Scaled down replica set hello-deploy-599bcfb9b9 to 6
#  Normal  ScalingReplicaSet  2m               deployment-controller  Scaled up replica set hello-deploy-bc445bff9 to 5
#  Normal  ScalingReplicaSet  2m               deployment-controller  Scaled down replica set hello-deploy-599bcfb9b9 to 5
#  Normal  ScalingReplicaSet  1m (x8 over 2m)  deployment-controller  (combined from similar events): Scaled down replica set hello-deploy-599bcfb9b9 to 0
```
Ok, it's time to **rollout**. We will roll back to revision #1(get it from revision list)
```powershell
kubectl rollout undo deployment hello-deploy --to-revision=1
#output
#deployment.apps "hello-deploy"

kubectl get deploy
#output
#NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
#hello-deploy   10        11        2            10          13m

kubectl rollout status deployment hello-deploy
#output
#Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
#Waiting for rollout to finish: 1 old replicas are pending termination...
#Waiting for rollout to finish: 1 old replicas are pending termination...
#Waiting for rollout to finish: 1 old replicas are pending termination...
#Waiting for rollout to finish: 9 of 10 updated replicas are available...
#Waiting for rollout to finish: 9 of 10 updated replicas are available...
#deployment "hello-deploy" successfully rolled out
```

_**EXAMPLE WITHOUT RECORD FLAG**_

Useful links:

* [Kubernetes in production](https://www.youtube.com/watch?v=-Ci4vd4rh4M)
* [Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
* [Running Web API using Docker and Kubernetes](https://blogs.msdn.microsoft.com/najib/2017/05/27/web-api-with-docker-and-kubernetes/)
* [Pluralsight - Getting Started Kubernetes](https://app.pluralsight.com/library/courses/getting-started-kubernetes/table-of-contents)
* [K8s Learning Scenario](https://www.katacoda.com/courses/kubernetes/add-additional-nodes-to-cluster)
* [Docker Cheat-Sheet](https://github.com/wsargent/docker-cheat-sheet)
* [Voting App with different components for Docker and Kubernetes](https://github.com/dockersamples/example-voting-app)
* [Kubernates Sample Apps](https://github.com/nigelpoulton/k8s-sample-apps)
* [Google instruction for Kubernates Sample App](https://cloud.google.com/kubernetes-engine/docs/tutorials/guestbook)
* [Kubernates Google Sample App](https://github.com/GoogleCloudPlatform/gifinator)
* [Amdatu Kubernetes Scalerd](https://bitbucket.org/amdatulabs/amdatu-kubernetes-scalerd)
* [Kubernetes Pods Livenes](https://kubernetes-v1-4.github.io/docs/user-guide/liveness/)

Additional Commands:
```powershell
kubectl expose deployment NAME --port 80 --type LoadBalancer
kubectl scale deployment NAME --replicas 3
kubectl create -f svc.yaml -f deployment.yaml

spec:
  type: LoadBalancer

kubectl delete pod NAME  #for show how replicas works

```

GitHub C# API Clients:

* [Kubernetes C# Client](https://github.com/kubernetes-client/csharp)
* [Kubernetes.DotNet](https://github.com/masroorhasan/Kubernetes.DotNet)
* [Orleans Clustering Provider for Kubernetes](https://github.com/OrleansContrib/Orleans.Clustering.Kubernetes)

**Don't store your Database in K8s!!!**