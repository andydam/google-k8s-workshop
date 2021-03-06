Getting Started with GCP
===============

This guide will take you through the steps necessary to continuously deliver your software to end users by leveraging [Google Container Engine](https://cloud.google.com/container-engine/) and [Jenkins](https://jenkins.io) to orchestrate the software delivery pipeline.

Objectives
----------

Before getting started, you first have to prepare the environment for the workshop.

1. Get a GCP account from the instructor
1. Connect to the Cloud Shell using the GCP account
1. Enable the necessary APIs
1. Set computing zone and project
1. Download the lab source code from GitHub

---

Google Cloud Platform Overview
------------------------------

- managed by Google
- provides basic resources like compute, storage and network
- also provides services like Cloud SQL and Kubernetes engine
- all operations can be done through the API
- SLAs define reliability guarantees for the APIs
- three ways of access
  - API calls
  - SDK commands
  - Cloud Console web UI

Google Cloud Computing service groups:

- Compute
- Storage
- Migration
- Networking
- Databases
- Developer Tools
- Management Tools

You will use these services while doing the lab:

- Kubernetes Engine: create Kubernetes cluster
- IAM & Admin: manage users and permissions
- Compute Engine: run virtual machines for worker nodes
- VPC Network: connectivity between the nodes
- Load Balancing: create Ingress of LoadBalancer type
- Persistent Disk: persistent volume for Jenkins
- Source Repositories: hosting source code for an app
- Cloud Build: build Docker containers
- Container Registry: storing versioned Docker images of an app

Cloud Console is admin UI for Google Cloud. With cloud console you can find and manage your resources through secure administrative interface.

Cloud console features:

- Resource Management
- Billing
- SSH in Browser
- Activity Stream
- Cloud Shell

Cloud SDK provides essential tools for cloud platform.

- Manage Virtual Machine instances, networks, firewalls, and disk storage
- Spin up a Kubernetes Cluster with a single command

Project

- Managing APIs
- Enabling billing
- Adding and removing collaborators
- Managing permissions for GCP resources

Zonal, Regional, and Global Resources

- Zone: instances and persistent disks
- Region: subnets and addresses
- Global: VPC Network and firewall

---

Google Cloud Platform (GCP) account
-----------------------------------

In this workshop, you will run Kubernetes in GCP. We have created a separate project for each student. You should receive an email with the credentials to log in.

We recommend using Chrome browser during the workshop.

1. Go to https://console.cloud.google.com/
1. Enter the username
1. Enter the user password

  Note: *Sometimes GCP asks for a verification code when it detects logins from unusual locations. It is security measure to keep the account protected. If this happens, please ask the instructor for the verification code.*

1. In the top left corner select the project "Cloud Project XX", where XX is your account number

Cloud Shell
-----------

Console is the UI tool for managing cloud resources. Most of the exercises in this course are done from the command line so you will need a terminal and an editor.

Click "Activate Cloud Shell" button in the top right corner.

  ![](docs/img/cloud-shell.png)

  ![](docs/img/cloud-shell-prompt.png)

Now click "Launch the editor" button.

This will start a virtual machine in the cloud and give you access to a terminal and an editor.

Enable APIs
-----------

As a project owner, you control which APIs are accessible for the project. Enable the APIs required for the workshop:

```
$ gcloud services enable --async \
  container.googleapis.com \
  compute.googleapis.com \
  containerregistry.googleapis.com \
  cloudbuild.googleapis.com \
  sourcerepo.googleapis.com \
  monitoring.googleapis.com \
  logging.googleapis.com \
  stackdriver.googleapis.com
```

The operation runs asynchronously. You can check if the APIs are enabled for the project, but enabling all these apis will take about 5m.

```
$ gcloud services list --enabled
```

You can also connect to status of job by running command suggested:

```
$ gcloud beta services operations wait operations/acf.xxxx-xxxx-xxxx-xxxx-xxxx
```

Once that completes or you waited about 5 minutes you can check services again:

```
$ gcloud services list --enabled
NAME                              TITLE
bigquery-json.googleapis.com      BigQuery API
cloudbuild.googleapis.com         Cloud Build API
compute.googleapis.com            Compute Engine API
container.googleapis.com          Kubernetes Engine API
containerregistry.googleapis.com  Container Registry API
logging.googleapis.com            Stackdriver Logging API
monitoring.googleapis.com         Stackdriver Monitoring API
oslogin.googleapis.com            Cloud OS Login API
pubsub.googleapis.com             Cloud Pub/Sub API
sourcerepo.googleapis.com         Cloud Source Repositories API
stackdriver.googleapis.com        Stackdriver API
storage-api.googleapis.com        Google Cloud Storage JSON API
```

Validate count:

```
$ gcloud services list --enabled|grep -v NAME|wc -l
12
```

If some APIs are not enabled retry in sync mode

```
$ gcloud services enable compute.googleapis.com
Waiting for async operation operations/tmo-acf.8c2c26e0-4997-4378-964f-fdce6d0b9fec to complete...
Operation finished successfully. The following command can describe the Operation details:
 gcloud services operations describe operations/tmo-acf.8c2c26e0-4997-4378-964f-fdce6d0b9fec
```

```
$ gcloud services list --enabled | grep compute
compute.googleapis.com             Compute Engine API
```

Set computing zone and region
-----------------------------

1. When the shell is open, set your default compute zone and region:

```shell
export PROJECT_ID=$(gcloud config get-value project)

export COMPUTE_REGION=us-west2
gcloud config set compute/region $COMPUTE_REGION

export COMPUTE_ZONE=us-west2-b
gcloud config set compute/zone $COMPUTE_ZONE

gcloud info
```

Note that changing the zone will not change the region automatically.

Every time you open new terminal you need to input these commands. Place them inside `~/.profile` file and they will be executed automatically each time you log in.

Download the lab source code from GitHub
------------------------------------

Clone the lab repository in your cloud shell, then `cd` into that dir:

  ```
  $ git clone https://github.com/Altoros/google-k8s-workshop.git
  Cloning into 'google-k8s-workshop'...
  Username for 'https://github.com': altoros-training
  Password for 'https://altoros-training@github.com':
  remote: Counting objects: 78, done.
  remote: Compressing objects: 100% (60/60), done.
  remote: Total 78 (delta 11), reused 78 (delta 11), pack-reused 0
  Unpacking objects: 100% (78/78), done.

  $ cd google-k8s-workshop
  ```
