# Lab 2
This excercise comes from lab 2 of the Linux Foundation LFD259 course. It creates a two-node Kubernetes cluster running on Ubuntu 20.04.

To download the lab materials run:
```
wget https://training.linuxfoundation.org/cm/LFD259/LFD259_V2023-02-28_SOLUTIONS.tar.xz --user=LFtraining --password=Penguin2014
tar -xvf LFD259_V2023-02-28_SOLUTIONS.tar.xz
```

## Setup VMs
Setup two Ubuntu 20.04 nodes using Vagrant.

```
vagrant up
```

## Run the Lab Commands
Run `k8scp.sh` on the control plane node and run `k8sworker.sh` on the worker node. Be sure to capture the join token from the `k8scp.sh` output.

Run the `kubeadm join` command on the worker to join the worker node to the cluster using the join token and hash printed from the control plane node. It should look something like this
```
sudo kubeadm join --token 118c3e.83b49999dc5dc034 \192.168.7.2:6443 --discovery-token-ca-cert-hash sha256:40aa946e3f53e38271bae24723866f56c86d77efb49aedeb8a70cc189bfe2e1d
```

You can remove the default taint on the control plane node if you want to allow containers to run on that node (by default they are not). Not typically done in production, but for small test cluster it can be helpful.
```
kubectl taint nodes --all node-role.kubernetes.io/control-plane
```

Use `basicpod.yml` and `basicservice.yml` manifests to deploy basic pod and service for a web server. Get familiar with `kubectl get` and `kubectl describe` output as well as the create and delete functions. `kubectl api-resources` is great way to see list of all available resource types.