# Uncomplicating Kubernetes
&nbsp;
## DAY-1: What is kubernetes?
&nbsp;

### Day-1 Contents

    - [What we will see today](#What-we-will-see-today?)
    - [Start of Day-1 class](#Start-of-Day-1-class)
    - [Which GNU/Linux distro should i use?](#Which-GNU/Linux-distro-should-i-use?)
    - [Some sites to visit](#Some-sites-to-visit)
    - [The Container Engine](#The-Container-Engine)
      - [OCI - Open Container Initiative](#oci---open-container-initiative)
      - [The Container Runtime](#The-Container-Runtime)
    - [What is Kubernetes?](#What-is-Kubernetes?)
    - [K8s architecture](#K8s-architecture)
    - [Ports we shoud worry about](#Ports-we-shoud-worry-about)
    - [K8s key concepts](#K8s-key-concepts)
    - [Installing and Customizing Kubectl](#[Installing-and-Customizing-Kubectl)
      - [Kubectl installing on GNU/Linux](#Kubectl-installing-on-GNU/Linux)
      - [Kubectl installing on MacOS](#[Kubectl-installing-on-MacOS)
      - [Kubectl installing on Windows](#Kubectl-installing-on-Windows)
    - [Customizing Kubectl](#Customizing-Kubectl)
      - [Auto-complete](#auto-complete)
      - [Create an Alias for Kubectl](#Create-an-Alias-for-Kubectl)
    - [Creating a Kubernetes Cluster](#Creating-a-Kubernetes-Cluster)
    - [Creating the cluster on your local machine](#Creating-the-cluster-on-your-local-machine)
      - [Kind](#kind)
        - [Installation on GNU/Linux](#Installation-on-GNU/Linux)
        - [Installation on MacOS](#Installation-on-MacOS)
        - [Installation on Windows](#Installation-on-Windows)
          - [Installation on Windows via Chocolatey](#Installation-on-Windows-via-Chocolatey)
        - [Create a cluster with Kind](#Create-a-cluster-with-Kind)
        - [Creating a cluster with multiples local nodes with Kind](#Creating-a-cluster-with-multiples-local-nodes-with-Kind)
    - [First Steps on the k8s](#First-steps-on-the-k8s)
        - [Checking the namespaces and pods](#Checking-namespaces-and-pods)
        - [Running our first pod on k8s](#Running-our-first-pod-on-k8s)
      - [Exposing the pod and creating a Service](#Exposing-the-pod-and-creating-a-Service)
      - [Cleaning up and going home](#Cleaning-up-and-going-home)

&nbsp;


### What we will see today?

During Day-1 we will explore the concept of containers, discuss the relevance of the container runtime and the container engine. In addition, we'll understand what Kubernetes is and its architeture, we'll talk about the control plane, workers, ApiServer, scheduler, controller and much more!
&nbsp;
### Start of Day-1 class
&nbsp;
### Which GNI/Linux distro shoul I use?

Considering that essential tools such as "systemd" and "journald" are now widely adotados as standard in the main distribuitions available, such as Ubuntu, Debian, CentOS, among others, you should face no difficulties when following the training using any of them
&nbsp;
### Some sites to visit

Bellow are the official Kubernetes project websites:

- [https://kubernetes.io](https://kubernetes.io)

- [https://github.com/kubernetes/kubernetes/](https://github.com/kubernetes/kubernetes/)

- [https://github.com/kubernetes/kubernetes/issues](https://github.com/kubernetes/kubernetes/issues)

&nbsp;
Below are the official pages for the Kubernetes certifications (CKA, CKAD and CKS):

- [https://www.cncf.io/certification/cka/](https://www.cncf.io/certification/cka/)

- [https://www.cncf.io/certification/ckad/](https://www.cncf.io/certification/ckad/)

- [https://www.cncf.io/certification/cks/](https://www.cncf.io/certification/cks/)

&nbsp;
### The Container Engine

Before we start talking a bit more about Kubernetes, we first need to understand some of the important components in the kubernetes ecosystem, one of which is the container engine

The *Container Engine* é o responsavel por gerenciar as imagens e volumes, ele é o responsavel por garantir que os recursos utilizados pelos containers estaão devidamente isolados, a vida do container, storage, rede, etc.

Until recently we only had Docker for this role. But today we have several options to use as a *Container Engine*

Options such as Docker, CRI-O and Podman are well known and prepared for the production environment. Docker is the most popular Container Engine and it uses containerd as its Container Runtime

Container Runtime? What is it?

I'll explain in a moment, but first we need to talk about the OCI. :)

&nbsp;
#### OCI - Open Container Initiative

OCI is a non-profit organization that aims to standardize the creation of containers so that they can be run in any environment. OCI was founded in 2015 by Docker, CoreOS, Google, IBM, Microsoft, Red Hat and VMware and is now part of the Linux Foundation.

runc, the main project developed by OCI, is a low-level runtime container widely used by various Container Engines, including Docker. This completely open source project is written in Go and its source code can be accessed on GitHub.

Now we can talk about what Container Runtime is

&nbsp;
#### The Container Runtime

In order to be able to run the containers on the nodes, it is necessary to have a *Container Runtime* installed on each of these nodes.

The *Container Runtime* is responsible for running the containers on the nodes. When you are using tools like Docker or Podman to run containers on your machine, for example, you are making use of a *Container Runtime*, or rather, your Container Engine is making use of a *Container Runtime*.

There are three types of *Container Runtime*:

- Low-level: these are the *Container Runtime* that are executed directly by the Kernel, such as runc, crun and runsc.

- High-level: these are *Container Runtimes* that are executed by a *Container Engine*, such as containerd, CRI-O and Podman.

- Sandbox and Virtualized: these are the *Container Runtime* that are run by a *Container Engine* and are responsible for running containers safely. Sandboxes run on unikernels or use a proxy to communicate with the kernel. gVisor is an example of a *Container Runtime* of the Sandbox type. The Virtualized type runs on virtual machines. Performance here is slightly lower than when running natively. Kata Containers is an example of a Virtualized *Container Runtime*.

&nbsp;
### What is Kubernetes?

The Kubernetes project was developed by Google in mid-2014 to act as a container orchestrator for the company. Kubernetes (k8s), which means “helmsman” in Greek, is an *open source* project with *design* and development based on the Borg project, which is also owned by Google [1](https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes/). Some other products available on the market, such as Apache Mesos and Cloud Foundry, also emerged from the Borg project.

As Kubernetes is a difficult word to pronounce - and to write - the community simply nicknamed it **k8s**, following the pattern [i18n](http://www.i18nguy.com/origini18n.html) (the letter “k” followed by eight letters and the “s” at the end), pronouncing it simply “kates”.


### K8s architecture

Like the other orchestrators available, k8s also follows a *control plane/workers* model, thus constituting a *cluster*, where at least three nodes are recommended for its operation: the *control-plane* node, responsible (by default) for managing the *cluster*, and the others as *workers*, responsible for running the applications.

It is possible to create a Kubernetes cluster running on just one node, but it is only recommended for study purposes and never run in a production environment.

If you want to use Kubernetes on your local machine, on your desktop, there are several solutions that will create a Kubernetes cluster, using virtual machines or Docker, for example.

With this you can have a Kubernetes cluster with several nodes, but all of them running on your local machine, on your desktop.

Some examples are:

* [Kind](https://kind.sigs.k8s.io/docs/user/quick-start): A tool for running Docker containers that simulate the operation of a Kubernetes cluster. It is used for teaching, development and testing purposes. Kind should not be used in production;

* [Minikube](https://github.com/kubernetes/minikube): tool for deploying a Kubernetes *cluster* locally with just one node. It is widely used for teaching, development and testing purposes. Minikube should not be used in production;

* [MicroK8S](https://microk8s.io): Developed by [Canonical](https://canonical.com), the same company that develops [Ubuntu](https://ubuntu.com). It can be used in various distributions and **can be used in production environments**, especially for *Edge Computing* and e IoT (*Internet of things*);

* [k3s](https://k3s.io): Developed by [Rancher Labs](https://rancher.com), it is a direct competitor to MicroK8s and can even be run on Raspberry Pi;

* [k0s](https://k0sproject.io): Developed by [Mirantis](https://www.mirantis.com), the same company that acquired the enterprise part of [Docker](https://www.docker.com). It's a distribution of Kubernetes with all the resources needed to work in a single binary, which makes it simple to install and maintain the cluster. The correct pronunciation is kay-zero-ess and its aim is to reduce the technical effort and wear and tear involved in installing a Kubernetes cluster, which is why its name alludes to *Zero Friction*. **k0s can be used in production environments;

* **API Server**: It is one of the main components of k8s. This component provides an API that uses JSON over HTTP for communication, for which the ``kubectl`` utility is mainly used by administrators to communicate with the other nodes. These communications between components are established via [REST] requests (https://restfulapi.net);

* **etcd**: The etcd is a distributed key-value *datastore* that k8s uses to store the specifications, status and configurations of the *cluster*. All data stored within etcd is only manipulated via the API. For security reasons, etcd is by default only run on nodes classified as *control plane* in the k8s *cluster*, but can also be run on external *clusters*, specific to etcd, for example;

* **Scheduler**: The *scheduler* is responsible for selecting the node that will host a given *pod* (the smallest unit of a k8s *cluster* - don't worry about that for now, we'll talk more about it later) to be executed. This selection is made based on the amount of resources available on each node, as well as the state of each of the nodes in the *cluster*, thus ensuring that the resources are well distributed. In addition, the selection of nodes on which one or more pods will run can also take into account user-defined policies, such as affinity, location of data to be read by applications, etc;

* **Controller Manager**: It is the *controller manager* that ensures that the *cluster* is in the last state defined in etcd. For example: if in etcd a *deploy* is configured to have ten replicas of a *pod*, it is the *controller manager* that will check if the current state of the *cluster* corresponds to this state and, if not, will try to reconcile the two;

* **Kubelet**: The *kubelet* plays the role of a k8s agent that runs on the worker nodes. On each worker node there must be a Kubelet agent running, in charge of effectively managing the *pods* directed by the *cluster*'s *controller* within the nodes. To do this, Kubelet can start, stop and keep the containers and pods running by following the instructions provided by the cluster controller;

* **Kube-proxy**: It acts as a *proxy* and a *load balancer*. This component is responsible for routing requests to the correct *pods*, as well as taking care of the network part of the node;

&nbsp;
### Ports we shoud worry about

**CONTROL PLANE**

Protocol|Direction|Port Range|Purpose|Used By
--------|---------|----------|-------|-------
TCP|Inbound|6443*|Kubernetes API server|All
TCP|Inbound|2379-2380|etcd server client API|kube-apiserver, etcd
TCP|Inbound|10250|Kubelet API|Self, Control plane
TCP|Inbound|10259|kube-scheduler|Self
TCP|Inbound|10257|kube-controller-manager|Self

* Every port marked by * is customizable, you need to make sure that the changed port is also open.


&nbsp;
**WORKERS**

Protocol|Direction|Port Range|Purpose|Used By
--------|---------|----------|-------|-------
TCP|Inbound|10250|Kubelet API|Self, Control plane
TCP|Inbound|30000-32767|NodePort|Services All

&nbsp;
### K8s key concepts

It's important to know that the way k8s manages containers is slightly different from other orchestrators, such as Docker Swarm, mainly due to the fact that it doesn't handle containers directly, but rather through *pods*. Let's get to know some of the main concepts surrounding k8s below:

- **Pod**: It is the smallest object in k8s. As previously mentioned, k8s does not work with containers directly, but organizes them within *pods*, which are abstractions that share the same resources, such as addresses, volumes, CPU cycles and memory. A pod can have several containers;

- **Deployment**: It is one of the main *controllers* used. Deployment*, together with ReplicaSet*, ensures that a certain number of replicas of a pod are running on the cluster's worker nodes. Deployment is also responsible for managing the lifecycle of applications, where characteristics associated with the application, such as image, port, volumes and environment variables, can be specified in *yaml* or *json* files and then passed as a parameter to ``kubectl`` to execute the deployment. This action can be performed for both creating and updating and removing the deployment;

- **ReplicaSets**: It is an object responsible for guaranteeing the number of pods running on the node;

- **Services**: It's a way for you to expose communication via a *ClusterIP*, *NodePort* or *LoadBalancer* to distribute requests between the various Pods in that Deployment. It works like a load balancer.


### Installing and Customizing Kubectl

#### Kubectl installing on GNU/Linux

Let's install ``kubectl`` with the following commands.

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```
&nbsp;
#### Kubectl installing on MacOS

``kubectl`` can be installed on MacOS using either [Homebrew](https://brew.sh) or the traditional method. With Homebrew already installed, kubectl can be installed as follows.

```
sudo brew install kubectl

kubectl version --client
```
&nbsp;
Or:

```
sudo brew install kubectl-cli

kubectl version --client
```
&nbsp;
With the traditional method, installation can be carried out with the following commands.

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"

sudo mv ./kubectl /usr/local/bin/kubectl

sudo chown root: /usr/local/bin/kubectl

kubectl version --client
```
&nbsp;
#### Kubectl installing on Windows

``kubectl`` can be installed by downloading it [at this link](https://dl.k8s.io/release/v1.29.1/bin/windows/amd64/kubectl.exe). 

Further information on how to install kubectl on Windows can be found [on this page](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/).


### Customizing Kubectl

#### Auto-complete

Execute o seguinte comando para configurar o alias e autocomplete para o ``kubectl``.

No Bash:

```bash
source <(kubectl completion bash) # sets up autocompletion in your current session (first, make sure you have installed the bash-completion package).

echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your shell.
```
&nbsp;
 ZSH:

```bash 
source <(kubectl completion zsh)

echo "[[ $commands[kubectl] ]] && source <(kubectl completion zsh)"
```
&nbsp;
#### Create an Alias for Kubectl

Create the alias ``k`` for ``kubectl``:

```
alias k=kubectl

complete -F __start_kubectl k
```
&nbsp;
### Creating a Kubernetes Cluster

### Creating the cluster on your local machine

We're going to show you a few options, in case you want to start playing with Kubernetes using only your local machine, your desktop.

#### Kind

Kind (*Kubernetes in Docker*) is another alternative for running Kubernetes in a local environment for testing and learning, but it is not recommended for production use.

##### Installation on GNU/Linux

To install it on GNU/Linux, run the following commands.

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64

chmod +x ./kind

sudo mv ./kind /usr/local/bin/kind
```
&nbsp;
##### Installation on MacOS

To install on macOS, run the following command.

```
sudo brew install kind
```
&nbsp;
ou

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-darwin-amd64
chmod +x ./kind
mv ./kind /usr/bin/kind
```
&nbsp;
##### Installation on Windows

To install it on Windows, run the following commands.
```
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.20.0/kind-windows-amd64
Move-Item .\kind-windows-amd64.exe c:\kind.exe
```
&nbsp;
###### Installation on Windows via Chocolatey

Run the following command to install Kind on Windows using Chocolatey.
```
choco install kind
```
&nbsp;
##### Create a cluster with Kind

After installing Kind, let's start our cluster.
```
kind create cluster

Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.24.0) 🖼
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/

```
&nbsp;
You can create more than one cluster and customize its name.

```
kind create cluster --name giropops

Creating cluster "giropops" ...
 ✓ Ensuring node image (kindest/node:v1.24.0) 🖼
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-giropops"
You can now use your cluster with:

kubectl cluster-info --context kind-giropops

Thanks for using kind! 😊
```
&nbsp;
To visualize your clusters using kind, run the following command.

```
kind get clusters
```
&nbsp;
Liste os nodes do cluster.

```
kubectl get nodes
```
&nbsp;
##### Creating a cluster with multiples local nodes with Kind

It is possible for this class to include multiple nodes in the Kind structure, which was mentioned earlier.

Run the following command to select and remove all local clusters created in Kind.

```
kind delete clusters $(kind get clusters)

Deleted clusters: ["giropops" "kind"]
```
&nbsp;
Create a configuration file to define how many and what types of nodes you want to create in the cluster. In the following example, the ``kind-3nodes.yaml`` configuration file will be created to specify a cluster with 1 control-plane node (which will run the control plane) and 2 workers.

```
cat << EOF > $HOME/kind-3nodes.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
EOF
```
&nbsp;
Now let's create a cluster called ``kind-multinodes`` using the specifications defined in the ``kind-3nodes.yaml`` file..

```
kind create cluster --name kind-multinodes --config $HOME/kind-3nodes.yaml

Creating cluster "kind-multinodes" ...
 ✓ Ensuring node image (kindest/node:v1.24.0) 🖼
 ✓ Preparing nodes 📦 📦 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-kind-multinodes"
You can now use your cluster with:

kubectl cluster-info --context kind-kind-multinodes

Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
```
&nbsp;
Validate the creation of the cluster with the following command.

```
kubectl get nodes
```
&nbsp;
More information about Kind is available at: https://kind.sigs.k8s.io

&nbsp;
### First steps on the k8s
&nbsp;

##### Checking namespaces and pods

k8s organizes everything within *namespaces*. Through them, security and resource limitations can be made within the *cluster*, such as *pods*, *replication controllers* and various others. To view the *namespaces* available in the *cluster*, type:
```
kubectl get namespaces
```
&nbsp;
Let's list the *pods* of the *namespace* **kube-system** using the following command.

```
kubectl get pod -n kube-system
```
&nbsp;
Is there a *pod* hidden in a *namespace*? You can list all the *pods* in all the *namespaces* with the following command.

```
kubectl get pods -A
```
&nbsp;
There is also the possibility of using the command with the ```o wide`` option, which provides more information about the resource, including which node the *pod* is running on. Example:

```
kubectl get pods -A -o wide
```
&nbsp;
##### Running our first pod on k8s

We're going to start our first *pod* on the k8s. To do this, run the following command.

```
kubectl run nginx --image nginx

pod/nginx created
```
&nbsp;
Listing the *pods* with ``kubectl get pods`` gives the following output.

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          66s
```
&nbsp;
Let's now remove our *pod* with the following command.

```
kubectl delete pod nginx
```
&nbsp;
The output should be something like:

```
pod "nginx" deleted
```
&nbsp;

##### Running our first pod on k8s using a manifest file


Another way to create a pod or any other object in Kubernetes is by using a manifest file, which is a file in YAML format where you pass all the definitions of your object. Later on, we'll talk more about how to build manifest files, but now I want you to know about the ``--dry-run`` option in ``kubectl``, with which we can simulate the creation of a resource and still have a manifest created automatically.  

To create a *pod* template:

```
kubectl run meu-nginx --image nginx --dry-run=client -o yaml > pod-template.yaml
```
&nbsp;
We are using the '-o' parameter to change the output to YAML format and redirecting the output to a file called 'pod-template.yaml'.

To create the *template* of a *deployment*:

With the generated file in hand, you can now create a pod using the manifest we created as follows:

```
kubectl apply -f pod-template.yaml
```

Don't worry about the 'apply' parameter just yet, we're going to talk about it in more detail, right now the important thing is that you know it's used to create new resources through manifest files.
&nbsp;

#### Exposing the pod and creating a Service

Devices outside the *cluster*, by default, cannot access the *pods* created, as is common in other container systems. To expose a *pod*, run the following command.

```
kubectl expose pod nginx
```

The following error message will be displayed:

```
error: couldn't find port via --port flag or introspection
See 'kubectl expose -h' for help and examples
```

The error occurs because k8s doesn't know which of the container's destination ports to expose (in this case, 80/TCP). To configure it, let's first remove our old *pod*:

```
kubectl delete -f pod-template.yaml
```

Now let's re-run the command to create the pod using the 'dry-run' parameter, adding the '--port' parameter to tell it which port the container is listening on, remembering that we are using nginx in this example, a webserver that listens on port 80 by default.

```
kubectl run meu-nginx --image nginx --port 80 --dry-run=client -o yaml > pod-template.yaml
kubectl create -f pod-template.yaml
```

List the pods.

```
kubectl get pods

NAME    READY   STATUS    RESTARTS   AGE
meu-nginx   1/1     Running   0          32s
```

The following command creates a k8s object called *Service*, which is used precisely to expose *pods* for external access.

```
kubectl expose pod meu-nginx
```

We can list all *services* with the following command.

```
kubectl get services

NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   8d
nginx        ClusterIP   10.105.41.192   <none>        80/TCP    2m30s
```

As you can see, there are two *services* in our *cluster*: the first is for use by k8s itself, while the second is the one we've just created.  

&nbsp;

#### Cleaning up and going home

To show all the newly created resources, you can use one of the following options.

```
kubectl get all

kubectl get pod,service

kubectl get pod,svc
```

Note that k8s provides us with some abbreviations for its features. Over time you will become familiar with them. To delete the resources you have created, you can execute the following commands.

```
kubectl delete -f pod-template.yaml
kubectl delete service nginx
```

List the resources again to check if they have been removed.


&nbsp;
