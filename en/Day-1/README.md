# Uncomplicating Kubernetes
&nbsp;
## DAY-1: What is kubernetes?
&nbsp;

### Day-1 Contents

- [Uncomplicating Kubernetes](#descomplicando-o-kubernetes)
  - [DAY-1](#day-1)
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
        - [Installation on GNU/Linux](#instalação-no-gnulinux)
        - [Installation on MacOS](#instalação-no-macos)
        - [Installation on Windows](#instalação-no-windows)
          - [Installation on Windows via Chocolatey](#instalação-no-windows-via-chocolatey)
        - [Create a cluster with Kind](#criando-um-cluster-com-o-kind)
        - [Creating a cluster with multiples local nodes with Kind](#criando-um-cluster-com-múltiplos-nós-locais-com-o-kind)
    - [First Steps on the k8s](#primeiros-passos-no-k8s)
        - [Checking the namespaces and pods](#verificando-os-namespaces-e-pods)
        - [Running our first pod on k8s](#executando-nosso-primeiro-pod-no-k8s)
        - [Running our first pod on k8s](#executando-nosso-primeiro-pod-no-k8s-1)
      - [Exposing the pod and creating a Service](#expondo-o-pod-e-criando-um-service)
      - [Cleaning up and going home](#limpando-tudo-e-indo-para-casa)

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

O Kind (*Kubernetes in Docker*) é outra alternativa para executar o Kubernetes num ambiente local para testes e aprendizado, mas não é recomendado para uso em produção.

##### Instalação no GNU/Linux

Para fazer a instalação no GNU/Linux, execute os seguintes comandos.

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64

chmod +x ./kind

sudo mv ./kind /usr/local/bin/kind
```
&nbsp;
##### Instalação no MacOS

Para fazer a instalação no MacOS, execute o seguinte comando.

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
##### Instalação no Windows

Para fazer a instalação no Windows, execute os seguintes comandos.

```
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.20.0/kind-windows-amd64
Move-Item .\kind-windows-amd64.exe c:\kind.exe
```
&nbsp;
###### Instalação no Windows via Chocolatey

Execute o seguinte comando para instalar o Kind no Windows usando o Chocolatey.

```
choco install kind
```
&nbsp;
##### Criando um cluster com o Kind

Após realizar a instalação do Kind, vamos iniciar o nosso cluster.

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
É possível criar mais de um cluster e personalizar o seu nome.

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
Para visualizar os seus clusters utilizando o kind, execute o comando a seguir.

```
kind get clusters
```
&nbsp;
Liste os nodes do cluster.

```
kubectl get nodes
```
&nbsp;
##### Criando um cluster com múltiplos nós locais com o Kind

É possível para essa aula incluir múltiplos nós na estrutura do Kind, que foi mencionado anteriormente.

Execute o comando a seguir para selecionar e remover todos os clusters locais criados no Kind.

```
kind delete clusters $(kind get clusters)

Deleted clusters: ["giropops" "kind"]
```
&nbsp;
Crie um arquivo de configuração para definir quantos e quais os tipos de nós que você deseja criar no cluster. No exemplo a seguir, será criado o arquivo de configuração ``kind-3nodes.yaml`` para especificar um cluster com 1 nó control-plane (que executará o control plane) e 2 workers.

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
Agora vamos criar um cluster chamado ``kind-multinodes`` utilizando as especificações definidas no arquivo ``kind-3nodes.yaml``.

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
Valide a criação do cluster com o comando a seguir.

```
kubectl get nodes
```
&nbsp;
Mais informações sobre o Kind estão disponíveis em: https://kind.sigs.k8s.io

&nbsp;
### Primeiros passos no k8s
&nbsp;

##### Verificando os namespaces e pods

O k8s organiza tudo dentro de *namespaces*. Por meio deles, podem ser realizadas limitações de segurança e de recursos dentro do *cluster*, tais como *pods*, *replication controllers* e diversos outros. Para visualizar os *namespaces* disponíveis no *cluster*, digite:

```
kubectl get namespaces
```
&nbsp;
Vamos listar os *pods* do *namespace* **kube-system** utilizando o comando a seguir.

```
kubectl get pod -n kube-system
```
&nbsp;
Será que há algum *pod* escondido em algum *namespace*? É possível listar todos os *pods* de todos os *namespaces* com o comando a seguir.

```
kubectl get pods -A
```
&nbsp;
Há a possibilidade ainda, de utilizar o comando com a opção ```-o wide```, que disponibiliza maiores informações sobre o recurso, inclusive em qual nó o *pod* está sendo executado. Exemplo:

```
kubectl get pods -A -o wide
```
&nbsp;
##### Executando nosso primeiro pod no k8s

Iremos iniciar o nosso primeiro *pod* no k8s. Para isso, executaremos o comando a seguir.

```
kubectl run nginx --image nginx

pod/nginx created
```
&nbsp;
Listando os *pods* com ``kubectl get pods``, obteremos a seguinte saída.

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          66s
```
&nbsp;
Vamos agora remover o nosso *pod* com o seguinte comando.

```
kubectl delete pod nginx
```
&nbsp;
A saída deve ser algo como:

```
pod "nginx" deleted
```
&nbsp;

##### Executando nosso primeiro pod no k8s utilizando um arquivo manifesto


Uma outra forma de criar um pod ou qualquer outro objeto no Kubernetes é através da utilizaçâo de uma arquivo manifesto, que é uma arquivo em formato YAML onde você passa todas as definições do seu objeto. Mas pra frente vamos falar mais sobre como construir arquivos manifesto, mas agora eu quero que você conheça a opção ``--dry-run`` do ``kubectl``, com ela podemos simular a criação de um resource e ainda ter um manifesto criado automaticamente. 

Para a criação do template de um *pod*:

```
kubectl run meu-nginx --image nginx --dry-run=client -o yaml > pod-template.yaml
```
&nbsp;
Estamos utilizando o parametro '-o' para modificar a saída para o formato YAML e redirecionando a saída para um arquivo chamado 'pod-template.yaml'.

Para a criação do *template* de um *deployment*:

Com o arquivo gerado em mãos, agora você consegue criar um pod utilizando o manifesto que criamos da seguinte forma:

```
kubectl apply -f pod-template.yaml
```

Não se preocupe por enquanto com o parametro 'apply', nós ainda vamos falar com mais detalhes sobre ele, nesse momento o importante é você saber que ele é utilizado para criar novos resources através de arquivos manifestos.

&nbsp;

#### Expondo o pod e criando um Service

Dispositivos fora do *cluster*, por padrão, não conseguem acessar os *pods* criados, como é comum em outros sistemas de contêineres. Para expor um *pod*, execute o comando a seguir.

```
kubectl expose pod nginx
```

Será apresentada a seguinte mensagem de erro:

```
error: couldn't find port via --port flag or introspection
See 'kubectl expose -h' for help and examples
```

O erro ocorre devido ao fato do k8s não saber qual é a porta de destino do contêiner que deve ser exposta (no caso, a 80/TCP). Para configurá-la, vamos primeiramente remover o nosso *pod* antigo:

```
kubectl delete -f pod-template.yaml
```

Agora vamos executar novamente o comando para a criação do pod utilizando o parametro 'dry-run', adicionando o parametro '--port' para dizer qual a porta que o container está escutando, lembrando que estamos utilizando o nginx nesse exemplo, um webserver que escuta por padrão na porta 80.

```
kubectl run meu-nginx --image nginx --port 80 --dry-run=client -o yaml > pod-template.yaml
kubectl create -f pod-template.yaml
```

Liste os pods.

```
kubectl get pods

NAME    READY   STATUS    RESTARTS   AGE
meu-nginx   1/1     Running   0          32s
```

O comando a seguir cria um objeto do k8s chamado de *Service*, que é utilizado justamente para expor *pods* para acesso externo.

```
kubectl expose pod meu-nginx
```

Podemos listar todos os *services* com o comando a seguir.

```
kubectl get services

NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   8d
nginx        ClusterIP   10.105.41.192   <none>        80/TCP    2m30s
```

Como é possível observar, há dois *services* no nosso *cluster*: o primeiro é para uso do próprio k8s, enquanto o segundo foi o quê acabamos de criar. 

&nbsp;

#### Limpando tudo e indo para casa

Para mostrar todos os recursos recém criados, pode-se utilizar uma das seguintes opções a seguir.

```
kubectl get all

kubectl get pod,service

kubectl get pod,svc
```

Note que o k8s nos disponibiliza algumas abreviações de seus recursos. Com o tempo você irá se familiar com elas. Para apagar os recursos criados, você pode executar os seguintes comandos.

```
kubectl delete -f pod-template.yaml
kubectl delete service nginx
```

Liste novamente os recursos para verificar se eles foram removidos.


&nbsp;
