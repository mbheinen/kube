# Lab 2
Download the lab materials
```
wget https://training.linuxfoundation.org/cm/LFD259/LFD259_V2023-02-28_SOLUTIONS.tar.xz --user=LFtraining --password=Penguin2014
tar -xvf LFD259_V2023-02-28_SOLUTIONS.tar.xz
```

# Google Cloud Platform infrastructure deploy
This directory contains infrastructure defintions for deploying simple k8s clusters to Google Cloud Platform on Google Elastic Compute (GCE) virtual machines.

You need to first install `gcloud` command line tool then run `gcloud init` if it is your first time using it. 

To deploy infrastructure, run:
```
gcloud deployment-manager deployments create ckad-deployment --config lab2.yaml
```
To view the status of the deploy:
```
gcloud deployment-manager deployments describe ckad-deployment
```
To delete the deployment:
```
gcloud deployment-manager deployments delete ckad-deployment
```
