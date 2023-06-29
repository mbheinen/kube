# Local Kubernetes Cluster
Ansible playbooks and other artifacts for setting up a Kubernetes cluster. Based on [ansible-for-kubernetes](https://github.com/geerlingguy/ansible-for-kubernetes/blob/master/cluster-local-vms/inventory).

First, install [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html), then ensure all hosts are accessible via Ansible. This is easy with 
an Ansible ad hoc command like `ansible -a 'hostname' all`

Then run:
```
ansible-galaxy install -r requirements.yml
```

Then setup cluter using.
```
ansible-playbook main.yml
```

## Managing Kubernetes
You can log into the control plane nod. From there, run `sudo su` to switch to the root user, and then you can use `kubectl` to manage the Kubernetes cluster.

## Running the `test-deployment.yml` playbook
You can run a playbook to run a test deployment and service in the cluster, and verify they are working correctly:
```
ansible-playbook test-deployment.yml
```
