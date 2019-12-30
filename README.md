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
  
## Kubernetes Component
> When you deploy Kubernetes, you get a cluster

> A cluster has at least one worker node and at least one master node

![K8S Component](https://d33wubrfki0l68.cloudfront.net/817bfdd83a524fed7342e77a26df18c87266b8f4/3da7c/images/docs/components-of-kubernetes.png)

### Master Component
- kube-apiserver
- etcd
- kube-scheduler
- kube-controller-manager
- cloud-controller-manager

### Worker Component
- kubelet
- kube-proxy
- Container Runtime

  
##### Further Reading
- [K8S by example](http://kubernetesbyexample.com/)
