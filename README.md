# Orange Pi 5 Kubernetes cluster

This playbook is for standing up my Orange Pi 5 cluster with and HA control plane and ability
to expose services via a load balancer, thanks to kube-vip.

# Roles

## Common

The common stuff to install things needed on all nodes, docker, kubeadm, etc...

## Master

The controller node role, this sets up the controller nodes to run with a stacked etcd setup with static manifests. This will install all the special stuff on the node defined with the `initial_master` key.

## Worker

Role for the worker nodes, this is a simpler setup as it just needs to join the nodes to the cluster for scheduling.

## Upgrade

This is run with the upgrade playbook to upgrade the nodes to the desired version.

You should at least sign on to the `initial_master` node, upgrade `kubeadm` to the desired version, then do a `kubeadm upgrade plan $VERSION` and make sure it is clean, and note down if it recommends any manual updates post upgrade.

# Background

This is not built to be a generic, anyone can use it, kind of thing, but if it helps someone
in some way, that's awesome.

This whole setup is massively overkill in almost every way for a home setup. It is impractical
was more expensive in the end than I anticipated, and finally pushed me to upgrade my home
network to 10Gbit. I'd do it again in a heartbeat as I had a lot of fun using all my tools
and skills from varying areas. I will be linking to a blog post once I'm all set with far
more detail.

As stated this cluster is using kube-vip in BGP mode to load balance the API server on the
control plane nodes, it is also setup to watch for Services that have request a loadbalancer. 
It uses a stacked etcd configuration, and the nodes stay tainted to
give breathing room to critical services. This is currently setup for 10 nodes, but I do plan
to expand if/when the Orange Pi 6 comes out, or I get the itch to hack on this more.

This is mostly an exercise to keep up with how to run a bare metal cluster as (thankfully) 
I am mostly only maintaining cloud offerings like EKS, AKS, and GKE. So the code may change
as I find new things to automate either here or in other repos.

## Deployment goals

- Ansible to orchestrate the installation and destruction if necessary
- kubeadm to spin up the nodes and handle upgrades
- kube-vip for handling HA and load balancing
- Cilium for networking
- Istio for service mesh
- nginx for ingress
- Argo for GitOps
- Rook for storage

## Hardware

The star of the show here is the Orange Pi 5 16GB model (the 32GB came out after I was deep into the build
phase of this project) which is a comfy 8 cores. We are using 10 of them in the cluster, tied together with
a switch with a 10Gbit uplink to the top of my network. Unfortunately the NIC on the OPi5 is only 1Gbit, but
it should more than suffice for home use.

It is all cooled with a single 360mm radiator, and pump/reservoir combo. Each SBC had a custom milled water
block which touches the important chips on the top side of the board. I wanted to cool the NVME disks as well
but was finding that too cumbersome, I may revisit that in the future. It is all tied together from a laser
cut board to mount the SBCs, piping, radiator, pump, switch 5v and 12v power supplies. So it only takes
a power feed, and an uplink, but is otherwise self contained.

I'd like to make it a bit fancier and add some OLEDs for some node readouts later, as well as some lighting
to really pull the look together, but this is a good starting point.
