# Commands Cheatsheet
Below are common commands needed to manage Kubernetes cluster.

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

## Kubernetes
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
```

You can use "-w" to watch kubectl commands

  `kubeclt get services -w`

Describe basic Kubernetes objects - Long list
```
kubectl describe pods
kubectl describe services/<service name>
kubectl describe deployment
```

All things go into "default" namespace unless you create and specify a different one

  `kubectl create namespace`

Create a new deployment

  `kubectl create deployment <name> --image=<image name>`

Can start proxy which exposes set of REST endpionts that can forward communication to cluster private network.

  `kubectl proxy`

Proxy will expose HTTP endpoints like this:
```
curl http://localhost:8001/version
curl http://localhost:8001/api/v1/namespaces/default/pods/<pod name>/
```

Anything application normally sends to stdout is considered a log. Access these with:

  `kubectl logs <pod name>`

Can execute commands in a pod:
```
kubectl exec <pod name> -- env
kubectl exec -it <pod name> -- bash (interactive TTY session:)
```

Create a new service

  `kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080`

Apply a new label:

  `kubectl label pods <pod name> version=v1`

