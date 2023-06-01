# Drupal Example
[Drupal](https://www.drupal.org/) uses [LAMP stack](https://www.ibm.com/cloud/learn/lamp-stack-explained). Below are examples of how to setup manaully or on a [Kubernetes](https://kubernetes.io/) cluster using [Helm](https://helm.sh/) with package from [Artifact Hub](https://artifacthub.io/).

## Manual Drupal Example
Use Vagrant to create a Ubuntu VM. Example in this repo assumes VirtualBox.

  `vagrant up`

Connect to VM

  `vagrant ssh`

Install Apache web server and MariaDB

  `sudo apt-update`

  `sudo apt install -y mariadb-server mariadb-client`

Run MariaDB configuration script to get things setup

  `sudo mysql_secure_installation`

Create database and user (with permissions) for Drupal in MariaDB. Then install php and dependencies.

  `sudo apt install -y php<and lots of packages it needs>`

  `sudo apt apache2 -y apache2 libapache2-mod-php`

Increase memory in PHP's config file (`/etc/php/7.4/apache2/php.ini`). 

Create vitual host configuration for Apache (`/etc/apache2/sites-available/drupal.conf`). Includes stuff like the directory for Apache web server to serve and other config.

Run `composer-setup.sh` to get PHP compser setup.

Set Linux permisisons on apache serve directory.

Create Drupal codebase

## Helm Drupal Example
To deploy with Helm chart:

[Install Helm](https://helm.sh/docs/intro/install/)

  `apt install helm`

Add Helm repository

  `helm repo add bitnami https://charts.binami.com/bitnami`

Install a release

  `helm install mysite bitnami/drupal`

Follow prompts to check status of the deployment. 

If on minikube, you can emulate load balancer using `minikube tunnel`

