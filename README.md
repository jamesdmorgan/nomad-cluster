# Hashicorp Nomad Docker Cluster

## Objective

* Create a cluster of virtual machines running Hashicorp *Nomad* and demonstrate
running docker containers across the cluster

## Features

* Vagrant provisioned virtualbox VMs
* Automation via Ansible
* Consul service discovery and cluster management
* Nomad UI visualisation

## Vagrant & Ansible

Vagrant is used to spin up a set of manager & worker VM nodes. By default we use
3 managers and 3 workers to demonstrate the clustering of both **Consul** and **Nomad**.

This setup uses *12Gb* of ram. This can be reduced to 1 manager and less workers if
there are limited resources

The playbooks are split into *bootstrap.yml* and *nomad.yml*. Currently swarm is also
enabled for containers outside the *Nomad* cluster but this may well be removed 
to simplify things. It could be useful to compare and contrast the different clustering
approaches.


## Nomad cluster

After running ```vagrant up``` the 6 virtual machines should be running the Nomad cluster
and consul. All Nomad nodes register with Consul. The number of expected servers is established
based on the number of manager VMs defined in the Vagrantfile.

Quorum is established and each Nomad node should be listed in Consul UI.

<img src="https://raw.githubusercontent.com/jamesdmorgan/nomad-cluster/master/images/consul-nomad.png" width="100%">

## Nomad UI


