# Commands Cheatsheet
Below are common commands needed to manage a Kubernetes cluster.

## kubectl
If using cloud provider, can set KUBECONFIG environment var for cluster config .yml

  `export KUBECONFIG=/path/to/file`

General admin commands:
```
kubectl version
kubectl cluster-info
```

List basic Kubernetes objects
```
kubectl get nodes
kubectl get pods
kubeclt get services
kubeclt get deployments
kubeclt get storageclass
```

Use `-o wide` to get wide output and use "-w" with kubectl commands to watch them similar to Linux `watch`.

  `kubeclt get services -w`

Describe basic Kubernetes objects - Long list
```
kubectl describe pods
kubectl describe services/<service name>
kubectl describe deployment
```

All things go into "default" namespace unless you create and specify a different one

  `kubectl create namespace <namespace>`

Delete namespace with

  `kubectl delete namespace <namespace>`

Set namespace context

  `kubectl config set-context --curent --namespace=<namespace>`

Create a new deployment

  `kubectl create deployment <name> --image=<image name>`

Deploy from a manifest

  `kubectl apply -f <manifest.yml>`

Edit a Deployment (e.g. increase/decrease replicas)

  `kubectl edit deployment <deployment>`

Start proxy which exposes set of REST endpoints that can forward communication to cluster private network.

  `kubectl proxy`

Proxy will expose HTTP endpoints like this:
```
curl http://localhost:8001/version
curl http://localhost:8001/api/v1/namespaces/default/pods/<pod name>/
```

Anything an application normally sends to stdout is considered a log. Access these with:

  `kubectl logs <pod name>`

Tail/follow logs of all pods in a deployed app with:

  `kubectl logs -f -l app=<app name>`

Execute commands in a pod:
```
kubectl exec <pod name> -- env
kubectl exec -it <pod name> -- bash (interactive TTY session:)
```

Create a new service

  `kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080`

Apply a new label:

  `kubectl label pods <pod name> version=v1`

Kubernetes Metrics API. Requires a metric server implementation to be installed, doesn't come with Kubernetes by default. For example, can find bitnami Helm chart for it. Once installed, view metrics with

```
kubectl top nodes
kubectl top pods
```

Autoscale with config in manifest or from `kubectl`. For example,

  `kubectl autoscale -n <namespace> deployment <deployment> --min=1 --max-8 --cpu-percent=50`

## minikube
[minikube](https://minikube.sigs.k8s.io/docs/) is great tool for local single node Kubernetes cluster.

```
minikube version
minikube start (start a cluser)
minikube ip (get ip)
minikube service <service name>
minikube delete
```

Can even emulate Load Balancer

  `minikube tunnel`