# Linux HA & MySQL Replication Guide

## Purpose

An web application must provide services without interruptions. If your application is hosted on one server and it suddenly shutdown your service is no longer available.
A simple solution to this problem is to set up a cluster compose of two nodes where one of them is active and response to requests when the other one is in standby listening to take over in case the active node failed.

We might also face another problem. For instance, lets think about a booking application which allow users to book a conferencing room. In our context of a high availability solution, lets presume a node shutdown. The other one takes over, we know it has its own MySQL instance, but we can ask ourselves is our reservations stored in that database?
We need to have any writes, updates and deletes operations made on the active node to be replicated in the other one in order to preserve data coherence. 
The solution to this problem is to replicate any changes made to the active node’s database to the passive one, so when the passive becomes active the data coherence is preserved and two users cannot book the same conference room for the same date anymore.


## Technical Solution
_This solution has been tested on VirtualBox using two instances of Ubuntu server 14.04 LTS._


The solution brings high availability in terms of:
* **networking** (Bonding channel)
* **storage** (RAID)
* **data coherence** (Database replication)
* **static files coherence** (Distributed file systems)

The tools used in this solution are :
* **Keepalived** (Failover)
* **Apache** (HTTP Server)
* **MySQL** (Replication)
* **GlusterFS** (Distributed file systems)
* **MDADM** (RAID)

## Terms & IP addresses used in this documentation

#### Node 1
* Server name: **m2lserver@node1**
* IP used for the communication between the nodes: **192.168.57.102**
* IP used to reach the server from outside the cluster: **10.54.0.10** 


#### Node 2
* Server name: **m2lserver@node2**
* IP used for the communication between the nodes: **192.168.57.103**
* IP used to reach the server from outside the cluster: **10.54.0.11** 

Virtual IP to reach the service: **10.54.0.100** 

#### Bondind-Channel
**eth0 and eth1:**
* Type: **Bond0** _load-balancing_
* Network: **192.168.57.0/24** 

**eth2 and eth3:**
* Type: **Bond1** _active-passive_
* Network: **10.54.0.0/24** 











