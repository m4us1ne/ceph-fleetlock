# Ceph Fleetlock
Ceph-Fleetlock is a Ceph Module that provides a failover Fleetlock API Server, to coordinate reboots of Fedora Coreos Nodes in a Ceph Cluster.



## Table of Contents
1. [Components](##Components)
2. [Prerequisites](##Prerequisites)
3. [Installation](##Installation)
4. [Alternative Deployments](#alternative-deployments)
5. [Future](#future)

## Components
This project contains two primary components:

FleetLock Server: A Python module designed to run a FleetLock-compatible server for managing reboots in a Ceph cluster.
Custom Container Service for Keepalived: A custom service container for managing Keepalived instances, ensuring high availability in a Ceph cluster environment.


## Prerequisites
* working/healthy Ceph Cluster managed by Cephadm
* Ceph Nodes Running Fedora Coreos


## Installation
### 1. Build Custom Ceph Container via the provided Dockerfile and Upload it to a Container Registry or use the Container Images of this Project

### 2. Upgrade you Ceph Cluster to use this 

```shell
ceph orch upgrade start --image $CUSTOM-CEPH-IMAGE
```

### 3. Enable Cephlock Manager Module

After a succesfull upgrade you can Check if the cephlock Module is available
``` shell
ceph mgr module ls
```
and enable it

```shell
ceph mgr module enable cephlock
```
### 4. Configure Cephlock Module
For Configuration changes to take effect disable and enable the module 

``` shell
ceph cephlock setup --after 01:20:24 --before 05:30:00 --port 8080 --enabled True
```
This Configuration would allow Reboots between 1:20:23 and 5:30 and the API Server to listen on Port 8080

### 4. Configure Failover
Edit the ceph-custom-container/keepalived-container.yaml to suit your needs.
Values that you should consider to change:
Line 18: Port should be same port that you configured in the previous Step.
Line 33: The IP address depends on your Ceph Cluster Setup
This file should adhere to the [Ceph Custom Container Reference](https://docs.ceph.com/en/reef/cephadm/services/custom-container/)

Copy your Configuration to the Ceph cluster and then apply it.
```shell
ceph orch apply -i /path/to/your/keepalived-container.yaml
```
### 5. Test Deployment
Check if IP is on the active manager node

Check if IP failovers, if you mark the manager as failed

See the [tests](tests/testing.md) Documentation for more tests.



### 6. Configure Zincatti
This Configuration should be a good starting point for your Cluster.
Cephlock supports Groups, you might want to add your OSD only Nodes in their own group.

```toml
[updates]
# Strategy for rolling out new versions
strategy = "fleet_lock"

[updates.fleet_lock]
# The cluster ip of the fleetlock service2
base_url = "http://10.0.15.81:8080/"
```




## Alternative Deployments

Other Deployments are possible, Keepalived could be run on outside of a container. 


## Future
Planned Features are:
* Logging lock/unlock requests
* Prometheus Metrics Endpoint
* Polish CLI
* Option to manually Release a lock
