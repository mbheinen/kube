# Lab 3
This excercise comes from lab 3 of the Linux Foundation LFD259 course.

To download the lab materials run:
```
wget https://training.linuxfoundation.org/cm/LFD259/LFD259_V2023-02-28_SOLUTIONS.tar.xz --user=LFtraining --password=Penguin2014
tar -xvf LFD259_V2023-02-28_SOLUTIONS.tar.xz
```

## Run the Lab Commands
`simple.py` is a very simple Python program that prints the current timestamp and hostname to a file every 5 seconds.

Build image:

```
docker build -t simpleapp .
```

Run container:
```
docker run simpleapp
```

Create a local registry:
```
kubectl create -f easyregistry.yml
```

Verify registry is working:
```
kubectl get svc registry
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
registry   ClusterIP   10.97.40.62   <none>        5000/TCP   28m
curl 10.97.40.62/v2/_catalog
{"repositories":[]}

```

There is a script in this repo to install podman, configure podman to work with non-TLS registry, and add the local registry.
The script is a bit dirty in that it leaves files it downloads and adds an evironment variable (`$repo`) to `.bashrc`.
```
. ./local-repo-setup.sh
```

Using the <dot> before the script is important so that variables set in the script are retained. One important variable set in
the script is $repo which is used in commands below.

You can then practice pulling or building images, tagging them, and pushing them to the local repo.

```
podman pull docker.io/library/alpine
podman tag alpine $repo/tagtest
podman push $repo/tagtest
podman tag simpleapp $repo/simpleap
podman push $repo/simpleapp
```

Remove locally cached images then practice pulling images from the local registry.
```
podman image rm alpine
podman image rm $repo/tagtest
podman pull $repo/tagtest
```

You can run `local-repo-setup.sh` on any k8s worker nodes to configure them to have podman installed and be able pull from
the local registry as well. Remember the registry is hosted on the k8s cluster.

Rebooting all nodes in the cluster *should* also make kubernetes aware of the local registry.
```
sudo reboot
```

You should also see the images you pushed in the local registry:
```
curl 10.97.40.62/v2/_catalog
{"repositories":["simpleapp"]}
```

Then you can create a deployment of the simple Python app on the k8s cluster.
```
kubectl create deployment app1 --image=$repo/simpleapp
kubectl scale deployment app1 --replicas=6
```

Running `kubectl get pods` should show 6 instances running. You can also save the deployment to a file:
```
kubectl get deployment app1 -o yaml > simpleapp.yaml
```

There is a modified version of `simpleapp.yaml` in this repository that adds a readinessProbe and a livenessProbe. The 
readinessProbe is simplistic to demonstrate how probes work. It simply checks for existance of an /tmp/healthy file. You
can manually make pods "ready" by doing `kubectl exec -it <pod name> -- touch /tmp/healthy`. The livenessProbe uses a 
sidecar goproxy hosted on port 8080.



