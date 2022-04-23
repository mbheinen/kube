# Drupal Example
[Drupal](https://www.drupal.org/) uses [LAMP stack](https://www.ibm.com/cloud/learn/lamp-stack-explained). Below are examples of how to setup manaully or on a [Kubernetes](https://kubernetes.io/) cluster using [Helm](https://helm.sh/) using package from [Artifact Hub](https://artifacthub.io/).

## Manual Drupal Example
Vagrantfile spins up Ubuntu VM. Example in this repo assumes VirtualBox.

  `vagrant up`

Connect to VM

  `vagrant ssh`

Install Apache web server and MariaDB

  `sudo apt-update`

  `sudo apt install -y mariadb-server mariadb-client`

Run MariaDB configuration script to get things setup

  `sudo mysql_secure_installation`

Create database and user (with permissions) for Drupal in MariaDB

  `sudo apt install -y php<and lots of packages it needs>`

  `sudo apt apache2 -y apache2 libapache2-mod-php`

Configure PHP (`/etc/php/7.4/apache2/php.ini`). Increase memory.

Create vitual host configuration for Apache (`/etc/apache2/sites-available/drupal.conf`). Includes stuff like the directory to serve and other config.

Run `composer-setup.sh` to get PHP compser setup.

Set Linux permisisons on apache serve directory.

Create Drupal codebase

## Helm Drupal Example
Be careful about using Helm because there is a lot of complexity that Helm and Kubernetes hide from you. While this hiding is done on purpose, it doesn't exonerate administrators from having to know and understand the complexity if you want a well tuned Kubernetes cluster, especially when running on cloud.

[Install Helm](https://helm.sh/docs/intro/install/)

  `apt install helm`

Add Helm repository

  `helm repo add bitnami https://charts.binami.com/bitnami`

Install a release

  `helm install mysite bitnami/drupal`

Follow prompts to check status of the deployment. 

If on minikube, you can emulate load balancer using `minikube tunnel`

## Kubernetes Manifest Example
Kubernetes Manifests are definition of Kubernetes objects that define the state of the cluster in a declarative way.

Good way to start is to copy an existing Kubernetes manifest or Helm chart and teak it. You'll get working examples running quickly while learning the configuration fields.

Separate objects based on how you want to manage (e.g. separate database from the app). Good practice to use labels and set resource limits even though these things aren't required. Use namespaces to organize related components of your application.

Kinds of Kubernetes objects involved:

  * PersistentVolumeClaim (PVC) - Stores state (e.g. volume for database content, drupal code resources). Most cloud Kubernetes providers have default storage mechanism (e.g. Block Volume on Linode).
  * Deployment - Stateless containerized component (e.g. MariaDB server process, fontend). Can reference/use PVCs to store their state. Can use initContainers to do pre-run tasks in a container (e.g. drupal themes setup which require  running drupal instance). Setup liveness and readiness probes which are basically heartbeat mechanisms.
  * Service - Exposes a Deployment. Can expose internal to the Kubernetes cluster or outside the cluster.
  * ConfigMap - Configuration such as variables, files, etc. Config maps can be directly mounted into Deployments. You can use template to build config file, change file permissions.
