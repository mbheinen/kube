# Local Kubernetes Cluster
Ansible playbooks and other artifacts for setting up a Kubernetes cluster. Based on [ansible-for-kubernetes](https://github.com/geerlingguy/ansible-for-kubernetes/blob/master/cluster-local-vms/inventory).

First, install [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

Then run:
```
ansible-galaxy install -r requirements.yml
```

After a few minutes, the three VMs will be booted, and the Ansible playbook will have installed Kubernetes using the `main.yml` playbook.

## Managing Kubernetes

You can log into the control plane node with the command:

    $ vagrant ssh kube1

From there, run `sudo su` to switch to the root user, and then you can use `kubectl` to manage the Kubernetes cluster.

## Running the `test-deployment.yml` playbook

You can run a playbook to run a test deployment and service in the cluster, and verify they are working correctly:

    $ ansible-playbook -i inventory test-deployment.yml
