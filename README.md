# Kubernetes
[Kubernetes](https://kubernetes.io/) notes repo used for reference and studying to hopefully get certification some day.

k8s - "kates"

`kubectl` - "koob e" or "koob" or "cube" plus "cuttle" or "C T L" or "control". It is the command line interface for working with a Kubernetes cluster.

Kubernetes doesn't exonerate administrators from having to thoroughly know and understand the complexity of the applications they're deploying. If you want a well tuned cluster, especially when running on cloud, it is very important to first understand the details of your application. Don't think to yourself that Kubernetes lets you reap all the benefits and skip the complexity. If anything it forces you to take time to deeply understand your architecture.

## History
Started as Google open source project announced in 2014. Google used lessons learned from Borg, their internal data center cluster management platform. Goal was to make running containers in production easier since complexity of deploying, monitoring, networking, etc. increases greatly when going from single machine to multiple distributed machines. [Google donated Kubernetes](https://techcrunch.com/2015/07/21/as-kubernetes-hits-1-0-google-donates-technology-to-newly-formed-cloud-native-computing-foundation-with-ibm-intel-twitter-and-others/) to Cloud Native Computing Foundation (CNCF), which is a project within the Linux Foundation, in July 2015.

Originally only supported Docker, but after Container Runtime Interface (CRI) and Open Container Initiatve (OCI) standardization things opened up a lot to allow other container runtimes.

## Architecture
Kubernetes clusters are made up of control plane node(s) and worker node(s). A control plane node runs the main manager (`kube-controller-manager`), the API server (`kube-apiserver`), a scheduler (`kube-scheduler`, optionally a cloud controller (`cloud-controller-manager`), and a datastore (e.g. `etcd`) which stores the state of the cluster, container settings, and the networking configuration.

![./high-level-architecture.png](!high-level-architecture.png "Kubernetes High Level Architecture")

  * `kube-apiserver` exposes a RESTful API for the cluster. You can communicate with it using the `kubectl` command line interface, write a custom client, or even use something like `curl` to interact with the API directly. Primary manager of the cluster.
  * `kube-scheduler` determines which is the best node to host a Pod of containers and uses an algorithm to do this. More details [here](https://github.com/kubernetes/kubernetes/blob/master/pkg/scheduler/scheduler.go).
  * `kube-controller-manager` main manager?
  * `node-controller` takes care of nodes in the cluster. Things like onboarding new nodes in the cluster, handling when nodes become unavailable.
  * `replication-controller` ensures desired number of containers are running.
  * `cloud-controller-manager` interacts with other tools, such as [Rancher](https://www.rancher.com/) or [DigitalOcean](https://www.digitalocean.com/) for third-party cluster management and reporting.

Every node in the system including the control plane and worker nodes run two containers, `kube-proxy` and `kubelet`. 

  * Container runtime like `containerd` or `cri-o` to run containers
  * `kubelet` container receives PodSpecs for container configuration, downloads and manages any necessary resources and works with the container runtime on the local node to ensure containers are running as well as handle error modes like restarting containers upon failure. A PodSpec is a JSON or YAML blob that describes a Pod. `kubelet` will work to configure the local node until the PodSpec has been met. It also sends back status to the `kube-apiserver` for eventual persistence. 
  * `kube-proxy` container creates and manages local firewall rules and networking configuration to expose containers on the network. This allows containers on the cluster to connect to eachother.

`kubeadm` is a command line tool for bootstrapping a cluster. It allows for easy deployment of a control plane and joining workers to the cluster. It can even setup multi-control plane cluster. `kubeadm` uses [Container Network Interface (CNI)](https://github.com/containernetworking/cni) specification as the default network interface mechanism. CNI is an emerging specification with associated libraries to write plugins that configure container networking and remove allocated resources when the container is deleted. Its aim is to provide a common interface between the various networking solutions and container runtimes. See more details [here](https://github.com/containernetworking/cni).

`crictl` used for debugging container runtimes. Works across all container runtimes unlike `ctr` and `nerdctl` which are just for Docker `containerd`. Not generally used for Kubernetes cluster administration.

## etcd
etcd is a key-value store used by Kubernetes to store stateful information about the cluster, container settings, and the networking configuration. It can be run as backing service on externan nodes or stacked on control plane notes.

`etcdctl` is a command line tool to interact with an etcd data store. Be aware that there are different versions of `etcdctl` and that `etcdctl` itself supports different API versions. Set the API version using the `ETCDCTL_API` environment variable. Commands are very different between version 2 and 3 of the API. See `etcdctl` usage for details.

## Pods
A Pod is one or more containers which share an IP address, access to storage and namespace. Typically, one container in a Pod runs a primary application, while other containers in the Pod support the primary application.

## Operators
Operators (sometimes called watch-loops or controllers) interrogate the `kube-apiserver` for a particular object state, modifying the object until the declared state matches the current state. One commonly used operator for containers is a Deployment. A Deployment deploys and manages a different operator called a ReplicaSet. A ReplicaSet is an operator which deploys multiple Pods, each with the same spec information. These are called replicas. There are many other Operators such as Jobs and CronJobs to handle single or recurring tasks. You can also write custom resource definitions and Operators.

Other kinds of Kubernetes resources:

  * PersistentVolumeClaim (PVC) - Stores state (e.g. volume for database content, drupal code resources). Most cloud Kubernetes providers have default storage mechanism (e.g. Block Volume on Linode). Lots of the default volumes only allow mounting to one container which means if you try to scale up the Deployment you'll see Kubernetes throw multi-mount errors.
  * Deployment - Stateless containerized component (e.g. MariaDB server process, fontend). Reference PVCs to store state. Use initContainers to do pre-run tasks in a container (e.g. drupal themes setup which require  running drupal instance). Setup liveness and readiness probes which are basically heartbeat mechanisms. Be careful not to set these too stringent.
  * Service - Exposes a Deployment. Can expose internal to the Kubernetes cluster or outside the cluster.
  * ConfigMap - Configuration such as variables, files, etc. Config maps can be directly mounted into Deployments. You can use template to build config file, change file permissions.
  * ClusterIssuer - Used to obtain things like certificates for exposed services.
  * CronJob - Run something periodically, similar to Linux Cron jobs. However, not super precise, for example it may run several seconds after your configured time so if you need very precise runs better to use different tool or build into your application.
  * DaemonSet - Ensure that a single Pod is deployed on every node. Often used for logging, metrics, and security pods
  * StatefulSet - Deploy Pods in a particular order, such that subsequent Pods are only deployed if previous Pods report a ready status. This can be useful for legacy applications which have runtime dependencies.

Context
A combination of user, cluster name and namespace. A convenient way to switch between combinations of permissions and restrictions. For example you may have a development cluster and a production cluster, or may be part of both the operations and architecture namespaces. This information is referenced from `~/.kube/config`.

Resource Limits
A way to limit the amount of resources consumed by a pod, or to request a minimum amount of resources reserved, but not necessarily consumed, by a pod. Limits can also be set per-namespaces, which have priority over those in the PodSpec.

Pod Security Admission
A beta feature to restrict pod behavior in an easy-to-implement and easy-to-understand manner, applied at the namespace level when a pod is created. These will leverage three profiles: Privileged, Baseline, and Restricted policies.

Network Policies
The ability to have an inside-the-cluster firewall. Ingress and Egress traffic can be limited according to namespaces and labels as well as typical network traffic characteristics.

## Manifests
Kubernetes Manifests are files that define a set of Kubernetes Operators that describe the state of the cluster in a declarative way.

When writing Manifests, separate Operators based on how you want to manage the cluster (e.g. separate database from the app). It is good practice to use labels and set resource limits even though these things aren't required. Labels are arbitrary strings which become part of the object metadata. These are selectors which can then be used when checking or changing the state of objects. A good way to start learning Manifests is to copy an existing Kubernetes Manifests or [Helm](https://helm.sh/) chart and teak it. You'll get working examples running quickly while learning the configuration fields.

A namespace is a segregation of resources, upon which resource quotas and permissions can be applied. Kubernetes objects may be created in a namespace or be cluster-scoped. Users can be limited by the object verbs allowed per namespace. Use namespaces to organize related components of your application. Kubernetes allows you to deploy into a namespace. If no namespace given, Kubernetes will deploy to the default namespace.

## Cloud Providers
Lots of cloud providers have fully managed Kubernetes clusters. Here are some:

 * [Linode LKE](https://www.linode.com/products/kubernetes/)
 * [Vultr Kubernetes Engine](https://www.vultr.com/pricing/#kubernetes-engine)
 * [Digital Ocean](https://www.digitalocean.com/products/kubernetes)
 * [Azure AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/)
 * [Google Cloud Platform GKE](https://azure.microsoft.com/en-us/services/kubernetes-service/)
 * [AWS EKS](https://aws.amazon.com/kubernetes/)

## Helm
Simple way to think of [Helm](https://helm.sh/) is that it's a way to share Kubernetes Manifests. These are called Helm charts and versions are referred to as "releases".

Be careful about using Helm early on in learning because there is a lot of complexity that Helm hides from you. While this hiding is done on purpose and part of its value add, it doesn't allow you to master Kubernetes.

## Ingress
Ingress is a Kubernetes managed way to configure inbound data requests to the cluster. It provides load balancing to set of backend services via an ingress controller. Can use [nginx](https://nginx.org/en/), [haproxy](https://www.haproxy.org/), [traefik](https://traefik.io/), etc. for the ingress controller.

For example, we can use Helm chart to install nginx ingress.

  `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`

  `helm install ingress-nginx ingress-nginx/ingress-nginx`

Shows up as another Kubernetes service. So you can find it with `kubectl get svc`

## External DNS
Kubernetes can hook directly into DNS provider using [ExternalDNS](https://github.com/kubernetes-sigs/external-dns).

## cert-manager
Can use [cert-manager](https://cert-manager.io/) to manage certificates for Kubernetes. Can install it a lot of ways, including Helm. After installing, you'll need to setup ClusterIssuer, which can be done via a Kubernetes manifest. See documentation.

## Rook
Simple way to think about [Rook](https://rook.io/) is that it's RAID for Kuberentes volumes. Similar to Helm, be careful because it is more complex than it first appears. There's a lot of layers of things going on with it. If you need it, it is a great tool but don't start here for 101 volume mounts. Most use cases can probably use a Network File System (NFS).

## Apache Bench
Apache Bench is a command line HTTP benchmark tool, `ab`. Great tool to quickly test scaled sites and APIs that use HTTP. It's part of the [Apache HTTP](https://httpd.apache.org/) project. You can install it with `apt install apache2-utils`.

## Operators
You can write your own operator for more complex applications/pods that you need to deploy.

## Sidecar
Use sidecar container when you need to run another service in support of an existing service or container. Since Kubernetes likes one service per container, it can get messy to try to run more than one service from a single container. This may come into play when you need something like a syslog server for a component. Run the syslog server in a sidecar container which will come up with the main container.

## Full VMs on Kubernetes
[kubevirt](https://kubevirt.io/) is one way to move existing Virtual Machine-based workloads that cannot be easily containerized to run on Kubernetes clusters. Can be helpful when migrating legacy applications.

## Logging
You can use one of the many cloud services to do this such as [Elastic](https://www.elastic.co/elastic-stack/), [sumo logic](https://www.sumologic.com/solutions/log-management/), or [Datadog](https://www.datadoghq.com/). Be aware of cost because it can grow quite large if you send absolutely every log to the service. Another option is to run your own Elastic, LogStash, Kibana (ELK) stack within the Kubernetes cluster. This really only works to a certain scale and can be problematic if the cluster if having issues because you may not be able to see logs.

## Monitoring
Cluster-wide metrics is not quite fully mature in native Kubernetes, so [Prometheus](https://prometheus.io/) is one common option used to gather metrics from nodes and even potentially some applications.

## Cluster Configuration
kubespray, kOps, or kubeadm to create Kubernetes cluster.


