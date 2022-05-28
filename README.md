# Kubernetes
[Kubernetes](https://kubernetes.io/) scratch notes repo used for reference and studying to hopefully get certification some day.

k8s - "kates"

`kubectl` - "koob e" or "koob" or "cube" plus "cuttle" or "C T L" or "control". It is the command line interface for working with a Kubernetes cluster.

Kubernetes doesn't exonerate administrators from having to thoroughly know and understand the complexity of the applications they're deploying. If you want a well tuned cluster, especially when running on cloud, it is very important to first understand the details of your application. Don't think to yourself that Kubernetes lets you reap all the benefits and skip the complexity. If anything it forces you to take time to deeply understand your architecture.

## Kubernetes Manifests
Kubernetes Manifests are definition of Kubernetes objects that define the state of the cluster in a declarative way.

Separate objects based on how you want to manage (e.g. separate database from the app). Good practice to use labels and set resource limits even though these things aren't required.

Use namespaces to organize related components of your application. Can deploy into a namespace. If no namespace given, Kubernetes will deploy to a default namespace.

Kinds of Kubernetes objects:

  * PersistentVolumeClaim (PVC) - Stores state (e.g. volume for database content, drupal code resources). Most cloud Kubernetes providers have default storage mechanism (e.g. Block Volume on Linode). Lots of the default volumes only allow mounting to one container which means if you try to scale up the Deployment you'll see Kubernetes throw multi-mount errors.
  * Deployment - Stateless containerized component (e.g. MariaDB server process, fontend). Reference PVCs to store state. Use initContainers to do pre-run tasks in a container (e.g. drupal themes setup which require  running drupal instance). Setup liveness and readiness probes which are basically heartbeat mechanisms. Be careful not to set these too stringent.
  * Service - Exposes a Deployment. Can expose internal to the Kubernetes cluster or outside the cluster.
  * ConfigMap - Configuration such as variables, files, etc. Config maps can be directly mounted into Deployments. You can use template to build config file, change file permissions.

Good way to start is to copy an existing Kubernetes manifest or [Helm](https://helm.sh/) chart and teak it. You'll get working examples running quickly while learning the configuration fields.

## Cloud Providers
Lots of cloud providers have fully managed Kubernetes clusters. Here are some:

 * [Linode LKE](https://www.linode.com/products/kubernetes/)
 * [Vultr Kubernetes Engine](https://www.vultr.com/pricing/#kubernetes-engine)
 * [Digital Ocean](https://www.digitalocean.com/products/kubernetes)
 * [Azure AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)
 * [Google Cloud Platform GKE](https://azure.microsoft.com/en-us/services/kubernetes-service/)
 * [AWS EKS](https://aws.amazon.com/kubernetes/)

## Helm
Simple way to think of [Helm](https://helm.sh/) is that it's a way to share Kubernetes manifests. These are called Helm charts and  versions are referred to as "releases".

Be careful about using Helm early on in learning because there is a lot of complexity that Helm hides from you. While this hiding is done on purpose and part of its value add, it doesn't allow you to master Kubernetes.

## Rook
Simple way to think about [Rook](https://rook.io/) is that it's RAID for Kuberentes volumes. Similar to Helm, be careful because it is more complex than it first appears. There's a lot of layers of things going on with it. If you need it, it is a great tool but don't start here for 101 volume mounts. Most use cases can probably use a Network File System (NFS).

## Full VMs on Kubernetes
[kubevirt](https://kubevirt.io/) is one way to move existing Virtual Machine-based workloads that cannot be easily containerized to run on Kubernetes clusters. Can be helpful when migrating legacy applications.

## Ingress
Ingress is a Kubernetes managed way to configure inbound data requests to the cluster. It provides load balancing to set of backend services via an ingress controller. Can use nginx, haproxy, 

For example, we can use Helm chart.

  `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`

  `helm install ingress-nginx ingress-nginx/ingress-nginx`



## Apache Bench
Apache Bench is a command line HTTP benchmark tool, `ab`. Great tool to quickly test scaled sites and APIs that use HTTP. It's part of the [Apache HTTP](https://httpd.apache.org/) project. You can install it with `apt install apache2-utils`. 