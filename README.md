# Awesome Kubernetes
A collection of kubernetes knowledge

## Brief Story of development
![back in time](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)
- Traditional deployment era
  - organizations ran applications on physical servers
  - caused resource allocation issues
  - scale by add more physical server but expensive for organizations to maintain many physical servers
- Virtualized deployment era
  - it allows you to run multiple Virtual Machines (VMs) on a single physical server’s CPU
  - with virtualization you can present a set of physical resources as a cluster of disposable virtual machines
  - full machine running all the components, including its own operating system
- Container deployment era
  - containers are similar to VMs, but they have relaxed isolation properties to share the Operating System (OS) among the applications

## Kubernetes Benefits
- Service discovery and load balancing
- Storage orchestration
- Automated rollouts and rollbacks
- Automatic bin packing
  - You tell Kubernetes how much CPU and memory (RAM) each container needs. Kubernetes can fit containers onto your nodes to make the best use of your resources.
- Self-healing
- Secret and configuration management
  - Lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and application configuration without rebuilding your container images, and without exposing secrets in your stack configuration
  
## Kubernetes Components
> When you deploy Kubernetes, you get a cluster

A cluster has at least one worker node and at least one master node

![K8S Component](https://d33wubrfki0l68.cloudfront.net/817bfdd83a524fed7342e77a26df18c87266b8f4/3da7c/images/docs/components-of-kubernetes.png)

### Master Component
- kube-apiserver
  - Exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.
- etcd
  - Consistent and highly-available key value store used as Kubernetes’ backing store for all cluster data
- kube-scheduler
  - Watches newly created pods that have no node assigned, and selects a node for them to run on
- kube-controller-manager
  - Logical 
    - Node Controller: Responsible for noticing and responding when nodes go down
    - Replication Controller: Responsible for maintaining the correct number of pods for every replication controller object in the system
    - Endpoints Controller: Populates the Endpoints object (that is, joins Services & Pods)
    - Service Account & Token Controllers: Create default accounts and API access tokens for new namespaces
- cloud-controller-manager
  - runs controllers that interact with the underlying cloud providers
  - allows the cloud vendor’s code and the Kubernetes code to evolve independently of each other

### Worker Component
- kubelet
  - An agent that runs on each node in the cluster. It makes sure that containers are running in a pod
- kube-proxy
  - a network proxy that runs on each node in your cluster to maintains network rules on nodes
  - These network rules allow network communication to your Pods from network sessions inside or outside of your cluster
- Container Runtime
  - The container runtime is the software that is responsible for running containers
  - [Read more](https://github.com/howtoautomateinth/awesome-docker)

## Kubernetes Object 
A Kubernetes object is a “record of intent”–once you create the object, the Kubernetes system will constantly work to ensure that object exists. By creating an object, you’re effectively telling the Kubernetes system what you want your cluster’s workload to look like; this is your cluster’s desired state

To work with Kubernetes objects–whether to create, modify, or delete them–you’ll need to use the Kubernetes API

### Object Spec and Status
Kubernetes object includes two nested object fields
- object spec 
  - which you must provide, describes your desired state for the object–the characteristics that you want the object to have
- object status
  - describes the actual state of the object, and is supplied and updated by the Kubernetes system

### Describing a Kubernetes Object
Most often, you provide the information to kubectl in a .yaml file. kubectl converts the information to JSON when making the API request

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

#### Name
Each object in your cluster has a Name that is unique for that type of resource. Every Kubernetes object also has a UID that is unique across your whole cluster

#### Namespace
Namespaces are intended for use in environments with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you should not need to create or think about namespaces at all

#### Label and Selector
Labels are key/value pairs that are attached to objects, such as pods. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users

```
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

> With label selector, the client/user can identify a set of objects. The label selector is the core grouping primitive in Kubernetes

```
"selector": {
    "component" : "redis",
}
```

#### Annotations
Labels can be used to select objects and to find collections of objects that satisfy certain conditions. In contrast, annotations are not used to identify and select objects.

Annotations allow you to add non-identifying metadata to Kubernetes objects. Examples include phone numbers of persons responsible for the object or tool information for debugging purposes

```
"metadata": {
  "annotations": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

#### Field Selectors
Field selectors let you select Kubernetes resources based on the value of one or more resource fields

```
kubectl get pods --field-selector status.phase=Running
```

#### Recommended Labels
Shared labels and annotations share a common prefix: app.kubernetes.io. Labels without a prefix are private to users. The shared prefix ensures that shared labels do not interfere with custom user labels

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  labels:
    app.kubernetes.io/name: mysql
    app.kubernetes.io/instance: wordpress-abcxzy
    app.kubernetes.io/version: "5.7.21"
    app.kubernetes.io/component: database
    app.kubernetes.io/part-of: wordpress
    app.kubernetes.io/managed-by: helm
```
## Kubernetes Object Type
### Workloads
#### Pods
![Pods](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)

> A Pod represents a unit of deployment: a single instance of an application in Kubernetes, which might consist of either a single container or a small number of containers that are tightly coupled and that share resources.

![Multiple Pods](https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg)
If you want to scale your application horizontally you should use multiple Pods, one for each instance as replication!

##### Pod Lifecycle
- Pending
  - The Pod accepted by the K8s system, but one or more of the container images not ready yet
- Running
  - The Pod bound to a node, and all of the containers created
- Succeeded
  - All Containers in the Pod have terminated in success
- Failed
  - All Containers in the Pod have terminated, and at least one Container has terminated in failure
- Unknown
  - For some reason the state of the Pod could not be obtained
  
#### ReplicaSet
A ReplicaSet’s purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods

#### ReplicationController
A ReplicationController ensures that a specified number of pod replicas are running at any one time. In other words, a ReplicationController makes sure that a pod or a homogeneous set of pods is always up and available

> A Deployment that configures a ReplicaSet is now the recommended way to set up replication.

#### Deployment
A Deployment provides declarative updates for Pods and ReplicaSets.

#### ConfigMap
is a Kubernetes abstraction meant to decouple environment-dependent application-configuration-data from containerized applications, allowing them to remain portable across environments

#### Secret
is an object that contains a small amount of sensitive data such as a password, a token, or a key. Putting such sensitive information in a secret allows for more control over how it is used and reduces the risk of accidental exposure

#### Stateful Set
is the workload API object used to manage stateful applications.require a Headless Service to be responsible for the network identity of the Pods.

Advantage
- Stable, unique network identifiers.
- Stable, persistent storage. (using volumeClaimTemplates field it will provide stable storage using PersistentVolumes provisioned)
- Ordered, graceful deployment and scaling.
- Ordered, automated rolling updates.
Use cases
- Database cluster (zookeeper,etc.)

### Storage
#### Persistent Volumes
- PersistentVolume (PV)
  - storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes
- PersistentVolumeClaim (PVC)
  - a request for storage by a user

Persistent Volumes are provisioned in two ways, Statically or Dynamically
- Static Volumes
  - A static PV simply means that some k8s administrator provisioned a persistent volume in the cluster
  - In other word, we will create Persistent Volumes (PV) and Persistent Volumes Calim (PVC)
- Dynamic Volumes
  - In some circumstances a pod could require a persistent volume that doesn’t exist
  - In other word, we only need to have Persistent Volumes Calim (PVC)

##### Claim Policies
- Retain
  - When the claim is deleted, the volume remains
- Recycle
  - When the claim is deleted the volume remains but in a state where the data can be manually recovered
- Delete
  - The persistent volume is deleted when the claim is deleted
  
### Services
is an abstract way to expose an application running on a set of Pods as a network service. Since the IP address of a pod is not a reliable way to access business functionality offered by the pod. 
The recommendation to access the pod outside the cluster is use [Ingress nginx](https://kubernetes.github.io/ingress-nginx/deploy/) with loadbalancer of Cloud provider like GKE or EKS

#### Headless Services
is a service with a service IP but instead of load-balancing it will return the IPs of our associated Pods. This allows us to interact directly with the Pods instead of a proxy.By set ClusterIP to NONE (Normally, you will interact with ClusterIP service that help you to redirect between Pods)

#### Endpoints
Endpoints track the IP Addresses of the objects the service send traffic to.
In normal cases, when we're using services object with selector so it will know how to send traffic to that endpoint but without selector it won't have endpoint to send the traffic to so that is a part we can use endpoint object to redirect traffic to anywhere depend on endpoint mapping manually 

##### Further Reading
- [K8S by example](http://kubernetesbyexample.com/)
