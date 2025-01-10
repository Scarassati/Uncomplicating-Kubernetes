# Uncomplicating Kubernetes
&nbsp;
## DAY-2 - Uncomplicating Pod
&nbsp;


&nbsp;
### Start of class Day-2
&nbsp;

### What we will see today

In today lesson, we'll look at all the important details abou the smallest kubernetes object, the Pod.
We'll see everything from creating a simple Pod, to Pods with multicontainers, with volumes and even with limited resource consumption, such as CPU or memory.
And of course, we'll learn how to see all the details of a running Pod and play around with our YAML files

### What is a Pod?


The pod is the smallest unit within a kubernetes cluster

When we're talking about Pods, we need to think of a Pod as a little box containing one or more containers. And these containers share the same resources as the Pod, such as IP, namespace, volume, etc.

So when we talk about Pod, we're talking about one or more containers that share the same resources, period.

&nbsp;

#### Creating a Pod

There are basically two way to create a Pod

Let's start by creating a Pod using a command in the terminal.

```bash
kubectl run giropops --image=nginx --port=80
```

the above command will create a Pod called giropops, with an image of nginx and port 80 exposed


#### Visualizing details about the Pods

to see the Pod created, we can use the command:

```bash
kubectl get pods
```

the above command will list all the Pods running in the cluster, in the default namespace

Yes, we have namespaces in Kubernetes, but that's a topic for another day. For now, let's focus on Pods and just know that by default, Kubernetes will create all objects within the default namespace if we don't specify another one

To see the Pods running in all namespaces, we can use the command:

```bash
kubectl get pods --all-namespaces
```

Or we can use the command:

```bash
kubectl get pods -A
```

Now, if you want to see all the Pods in a specific namespace, you can use the comand:

```bash
kubectl get pods -n <namespace>
```

For example:

```bash
kubectl get pods -n kube-system
```

The above command will list all the Pods that are running in the kube-system namespace, which is the namespace where Kubernetes will create all the objects related to the cluster, such as the CoreDNS Pods, the Kube-Proxy Pods, the Kube-Controller-Manager, the Kube-Scheduler Pods, etc.

If you want to see even more details about the Pod, you can ask Kubernetes to display the Pod's details in YAML format using the command:

```bash
kubectl get pods <nome-do-pod> -o yaml
```

For example:

```bash
kubectl get pods giropops -o yaml
```

The command above will show all the Pod's details in YAML format, practically the same as what you'll see in the YAML file we'll use next to create the Pod. However, it will have a few more details, such as the Pod's UID, the name of the Node where the Pod is running, etc. After all, this Pod is already running, so Kubernetes already has more details about it.

Another interesting output is the output in JSON format, which you can see using the command:

```bash
kubectl get pods <nome-do-pod> -o json
```

For example:

```bash
kubectl get pods giropops -o json
```

Using the ''-o'' parameter, you can choose the output format you want to see, for example, YAML, JSON, wide, etc.

Ahh, the wide output is interesting because it shows more details about the Pod, such as the Pod's IP and the Node where the Pod is running

```bash
kubectl get pods <nome-do-pod> -o wide
```

for example

```bash
kubectl get pods giropops -o wide
```

Now, if you want to see the Pod's details, but without having to use the get command, you can use the command:

```bash
kubectl describe pods <nome-do-pod>
```

For example:

```bash
kubectl describe pods giropops
```

With 'descibe' you can see all the details of the Pod, including the details of the container inside the Pod


#### Removing a Pod

Now let's remove the Pod we created, using the command:

```bash
kubectl delete pods giropops
```

Easy, right? Now, Let'go create the Pod through a Yaml file

&nbsp;

#### Create a Pod through a Yaml file

Let's create a Yaml file called pod.yaml with the following content

```yaml
apiVersion: v1 # versão da API do Kubernetes
kind: Pod # object type that we are creating
metadata: # Pod metadata
  name: giropops # Pod name that we are creating
  labels: # labels do Pod
    run: giropops # label run with the value giropops
spec: # Pod specifications
  containers: # containers inside the Pod
  - name: giropops # Container name
    image: nginx # Container image
    ports: # ports that are being exposed by the container
    - containerPort: 80 # port 80 exposed by the container
```

Now, Let's create the pod using YAML filme that we have just created.

```bash
kubectl apply -f pod.yaml
```

The above command will create the Pod using the YAML file we created.

To see the Pod created we can use the command:

```bash
kubectl get pods
```

Since we're using the `apply` command, I think it's worth explaining what it does.

The `apply` command is a command that does what the name says, it applies the YAML file to the cluster, that is, it creates the object that is described in the YAML file in the cluster. If the object already exists, it will update it with the information in the YAML file.

Another command you can use to create an object in the cluster is the `create` command, which also creates the object described in the YAML file in the cluster, but if the object already exists, it will return an error. That's why the `apply` command is more commonly used, because it updates the object if it already exists. :)

Now, let's look at the details of the Pod we've just created.

```bash
kubectl describe pods giropops
```

#### Viewing the Pod logs

Another very useful command to see what is happening with the Pod, more specifically to see what the container is logging, is the command:

```bash
kubectl logs giropops
```

Where giropops is the name of the Pod we created.

If you want to see the container logs in real time, you can use the command:

```bash
kubectl logs -f giropops
```

Simple, right? Now, let's remove the Pod we created, using the command:

```bash
kubectl delete pods giropops
```

&nbsp;

#### Creating a Pod with more than one container

Let's create a YAML file called pod-multi-container.yaml with the following content:

```yaml
apiVersion: v1 # APIVersion the kubernetes
kind: Pod # object type that we are creating
metadata: # Pods metadata
  name: giropops # name the pods that we are creating
  labels: # Pod labels
    run: giropops # label run with the value giropops
spec: # Pod specifications
  containers: # Container inside the Pod
  - name: girus # Container name
    image: nginx # Container image
    ports: # ports that are being exposed by the container
    - containerPort: 80 # port 80 exposed by the container
  - name: strigus # Container name
    image: alpine # Container image
    args:
    - sleep
    - "1800"
```

With the manifest above, we are creating a Pod with two containers, a container called girus with the nginx image and another container called strigus with the alpine image. An important thing to remember is that the Alpine container is being created with the `sleep 1800` command so that the container doesn't stop running, unlike the Nginx container which has a main process running in the foreground, so that the container doesn't stop running.

Alpine is a Linux distribution that is very light, and doesn't have a main process running in the foreground, so we need to run the `sleep 1800` command so that the container doesn't stop running, thus adding a main process that runs in the foreground.

Now, Let's create the pod using YAML filme that we have just created.

```
kubectl apply -f pod-multi-container.yaml
```

To see the Pod created, we can use the command:

```bash
kubectl get pods
```

Now, let's look at the details of the Pod we've just created.

```bash
kubectl describe pods giropops
```

### Comands `attach` and `exec`

Let's get to know two new commands, `attach` and `exec`.

The `attach` command is used to connect to a container that is running inside a Pod. For example, let's connect to the Alpine container that is running inside the Pod we created.

```bash
kubectl attach giropops -c strigus
```

Using `attach` is as if we were connecting directly to a virtual machine console, we're not creating any processes inside the container, we're just connecting to it.

For this reason, if we try to use `attach` to connect to the container that is running Nginx, we will connect to the container and we will be stuck with the Nginx process that is running in the foreground, and we won't be able to execute any other commands.

```bash
kubectl attach giropops -c girus
```

To exit the container, simply press `Ctrl + C`.

Got it? We're only going to use `attach` to connect to a container that's running inside a Pod, not to execute commands inside the container.

Now, if you want to execute commands inside the container, you can use the `exec` command.

The `exec` command is used to execute commands inside a container that is running inside a Pod. For example, let's run the `ls` command inside the Alpine container that is running inside the Pod we created.

```bash
kubectl exec giropops -c strigus -- ls
```

We can also use `exec` to connect to a container that is running inside a Pod, but to do this we need to pass the `-it` parameter to the `exec` command.

```bash
kubectl exec giropops -c strigus -it -- sh
```

The `-it` parameter is used so that the `exec` command creates a process inside the container with interactivity and a terminal, making the `exec` command behave like the `attach` command, but with the difference that the `exec` command creates a process inside the container, in this case the `sh` process. This is why the `exec` command is most commonly used, because it creates a process inside the container, unlike the `attach` command, which doesn't create any processes inside the container.

In this case, we can even connect to the Nginx container, because it will connect to the container by creating a process that is our `sh` command interpreter, and we can execute any command inside the container because we have a shell to interact with the container.

```bash
kubectl exec giropops -c girus -it -- sh
```

To exit the container, simply press `Ctrl + D`.

Don't forget to delete the Pod we created.

```bash
kubectl delete pods giropops
```

&nbsp;

### Create the Pods with limits memory and CPU

Let's create a YAML file called pod-limited.yaml with the following content:

```yaml
apiVersion: v1 # ApiVersion Kubernetes
kind: Pod # object type that we are creating
metadata: # Pod metadata
  name: giropops # name the pods that we are creating
  labels: # Pod label
    run: giropops # label run with the giropops value
spec: # Pod specifications
  containers: # containers inside the Pod 
  - name: girus # Container name
    image: nginx # Container image
    ports: #  ports that are being exposed by the container
    - containerPort: 80 # port 80 exposed by the container
    resources: # resources being used by the container
      limits: # maximum resource limits that the container can use
        memory: "128Mi" # memory limit being used by the container, in this case 128 megabytes maximum 
        cpu: "0.5" # maximum CPU limit that the container can use, in this case 50% of a CPU maximum
      requests: # resources guaranteed to the container
        memory: "64Mi" # guaranteed memory for the container, in this case 64 megabytes
        cpu: "0.3" # CPU guaranteed to the container, in this case 30% of a CPU
```

See that we are getting to know some new fields, `resources`, `limits` and `requests`.

The `resources` field is used to define the resources that will be used by the container, and within it we have the `limits` and `requests` fields.

The `limits` field is used to define the maximum resource limits that the container can use, and the `requests` field is used to define the resources guaranteed to the container.

Too simple! 

The values we passed to the `limits` and `requests` fields were:

- `memory`: amount of memory the container can use, for example, `128Mi` or `1Gi`. The value `Mi` means mebibytes and the value `Gi` means gibibytes. The value `M` stands for megabytes and the value `G` for gigabytes. The value `Mi` is used to define the memory limit in mebibytes, because Kubernetes uses the binary unit system, not the decimal unit system. The value `M` is used to define the memory limit in megabytes, because Docker uses the decimal unit system, not the binary unit system. So, if you're using Docker, you can use the `M` value to set the memory limit, but if you're using Kubernetes, you should use the `Mi` value to set the memory limit.

- `cpu`: amount of CPU the container can use, for example, `0.5` or `1`. The value `0.5` means 50% of a CPU, and the value `1` means 100% of a CPU. The value `m` stands for millicpu, i.e. millicpu equals 1/1000 of a CPU. So, if you want to set the CPU limit to 50% of a CPU, you can set the value `500m`, or you can set the value `0.5`, which is the same as setting the value `500m`.

Now let's create the Pod with the memory and CPU limits.

```bash
kubectl create -f pod-limitado.yaml
```

Now let's check that the Pod has been created.

```bash
kubectl get pods
```

Let's check the details of the Pod.

```bash
kubectl describe pod giropops
```

You can see that the Pod has been created successfully, and that the memory and CPU limits have been set according to the YAML file. 

Below is the part of the output from the `describe` command that shows the memory and CPU limits.

```bash
Containers:
  girus:
    Container ID:   docker://e7b0c7b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b0b
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 01 Jan 2023 00:00:00 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        300m
      memory:     64Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-0b0b0 (ro)
```

See that in the output above, it shows the CPU field with the value `500m`, which means that the container can use a maximum of 50% of a CPU, after all one CPU is equal to 1000 milliCPUs, and 50% of 1000 millicpus is 500 milliCPUs.

To test the memory and CPU limits, you can run the `stress` command inside the container, which is a command that makes the container consume CPU and memory resources. Remember to install the `stress` command, as it is not installed by default.

To make it easy to test, let's create a Pod with Ubuntu with limited memory, and let's install the `stress` command inside the container.

Create the file `pod-ubuntu-limited.yaml`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    args:
    - sleep
    - infinity
    resources:
      limits:
        memory: "128Mi"
        cpu: "0.5"
      requests:
        memory: "64Mi"
        cpu: "0.3"
```

Look at the `infinity` parameter, it makes the container wait forever and thus keeps it running.

Now let's create the Pod.

```bash
kubectl create -f pod-ubuntu-limitado.yaml
```

Now let's check that the Pod has been created.

```bash
kubectl get pods
```

Now let's go inside the container.

```bash
kubectl exec -it ubuntu -- bash
```

Now let's install the `stress` command.

```bash
apt update
apt install -y stress
```

Now let's run the `stress` command to consume memory.

```bash
stress --vm 1 --vm-bytes 100M
```

So far so good, because we've set the memory limit to 128Mi, and the `stress` command is consuming 100M, so everything is fine.

Let's increase the memory consumption to 200M.

```bash
stress --vm 1 --vm-bytes 200M
```

See that the `stress` command can't consume 200M, because the memory limit is 128Mi, and 128Mi is less than 200M, so we get an error and the `stress` command is interrupted.

We've reached our goal, we've reached the limit of our container! :D

Want to play with the `stress` command a bit more? Take a look at its `--help`.

```bash
stress --help
```

It has several options for you to play around with memory and CPU consumption.

&nbsp;

### Adding an EmptyDir volume to Pod

First of all, this isn't the time to go into more detail about volumes, we'll have a whole day to talk about volumes, so don't worry about that now.

Today is for us to get very comfortable with Pods, from their creation, administration, command execution, etc.

So, let's create a Pod with an EmptyDir volume.

First, what is an EmptyDir volume?

An EmptyDir volume is a volume that is created when the Pod is created, and it is destroyed when the Pod is destroyed, i.e. it is a temporary volume.

On a day-to-day basis, you won't use this type of volume much, but it's important that you know it exists. One of the most common use cases is when you need to share data between the containers in a Pod. Imagine you have two containers in a Pod and one of them has a directory with data, and you want the other container to have access to that data. In this case, you can create a volume of type EmptyDir and share this volume between the two containers.

Call the file `pod-emptydir.yaml`.

```yaml
apiVersion: v1 
kind: Pod 
metadata: 
  name: giropops 
spec: 
  containers: 
  - name: girus  
    image: ubuntu
    args: 
    - sleep 
    - infinity 
    volumeMounts: 
    - name: primeiro-emptydir 
      mountPath: /giropops  
  volumes: # lista de volumes
  - name: primeiro-emptydir 
    emptyDir: 
      sizeLimit: 256Mi 
```

Now let's create the Pod.

```bash
kubectl create -f pod-emptydir.yaml
```

Now let's check that the Pod has been created.

```bash
kubectl get pods
```

You can view the output of the `kubectl describe pod giropops` command to see the volume that was created.

```bash
kubectl describe pod giropops
```

```bash
Volumes:
  primeiro-emptydir:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  256Mi
```

Now let's go inside the container.

```bash
kubectl exec -it giropops -- bash
```

Now let's create a file in the `/giropops` directory.

```bash
touch /giropops/FUNCIONAAAAAA
```

That's it, our file has been created inside the `/giropops` directory, which is a directory inside a volume of type EmptyDir.

If you type `mount`, you'll see that the `/giropops` directory is mounted correctly inside our container.

There, now you know how to create a Pod with an EmptyDir volume. :)

Remember once again that we're still going to see much, much more about volumes, so don't worry about that now.

&nbsp;
