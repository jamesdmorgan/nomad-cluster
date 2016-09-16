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

### Nomad server

```
Sep 16 15:30:11 manager3 systemd[1]: Started Nomad.
Sep 16 15:30:11 manager3 systemd[1]: Starting Nomad...
Sep 16 15:30:12 manager3 nomad[17512]: Loaded configuration from /etc/nomad.d/nomad-server.hcl
Sep 16 15:30:12 manager3 nomad[17512]: ==> Starting Nomad agent...
Sep 16 15:30:12 manager3 nomad[17512]: ==> Nomad agent configuration:
Sep 16 15:30:12 manager3 nomad[17512]: Atlas: <disabled>
Sep 16 15:30:12 manager3 nomad[17512]: Client: false
Sep 16 15:30:12 manager3 nomad[17512]: Log Level: INFO
Sep 16 15:30:12 manager3 nomad[17512]: Region: global (DC: dc1)
Sep 16 15:30:12 manager3 nomad[17512]: Server: true
Sep 16 15:30:12 manager3 nomad[17512]: ==> Nomad agent started! Log data will stream in below:
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12 [INFO] raft: Node at 192.168.56.103:4647 [Follower] entering Follower state (Leader: "")
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12 [INFO] serf: EventMemberJoin: manager3.global 192.168.56.103
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12.168370 [INFO] nomad: starting 2 scheduling worker(s) for [service batch system _core]
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12 [WARN] serf: Failed to re-join any previously known node
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12.174617 [INFO] nomad: adding server manager3.global (Addr: 192.168.56.103:4647) (DC: dc1)
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12 [INFO] serf: EventMemberJoin: manager1.global 192.168.56.101
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12 [INFO] serf: EventMemberJoin: manager2.global 192.168.56.102
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12.319912 [INFO] nomad: adding server manager1.global (Addr: 192.168.56.101:4647) (DC: dc1)
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12.319982 [INFO] nomad: Attempting bootstrap with nodes: [192.168.56.101:4647 192.168.56.102:4647 192.168.56.103:4647]
Sep 16 15:30:12 manager3 nomad[17512]: 2016/09/16 15:30:12.320593 [INFO] nomad: adding server manager2.global (Addr: 192.168.56.102:4647) (DC: dc1)
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16.653295 [INFO] server.consul: successfully contacted 2 Nomad Servers
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16 [WARN] raft: Heartbeat timeout from "" reached, starting election
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16 [INFO] raft: Node at 192.168.56.103:4647 [Candidate] entering Candidate state
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16 [WARN] raft: Remote peer 192.168.56.102:4647 does not have local node 192.168.56.103:4647 as a peer
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16 [INFO] raft: Election won. Tally: 2
Sep 16 15:30:16 manager3 nomad[17512]: 2016/09/16 15:30:16 [INFO] raft: Node at 192.168.56.103:4647 [Leader] entering Leader state
```

Quorum is established and each Nomad node should be listed in Consul UI.

<img src="https://raw.githubusercontent.com/jamesdmorgan/nomad-cluster/master/images/consul-nomad.png" width="100%">

<img src="https://raw.githubusercontent.com/jamesdmorgan/nomad-cluster/master/images/consul-redis-instance.png" width="100%">
## Nomad UI


<img src="https://raw.githubusercontent.com/jamesdmorgan/nomad-cluster/master/images/nomad-ui-redis.png" width="100%">
