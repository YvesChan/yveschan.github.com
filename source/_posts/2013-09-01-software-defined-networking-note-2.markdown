---
layout: post
title: "Software Defined Networking - Note 2"
date: 2013-09-01 21:49
comments: true
categories: [SDN, OpenFlow]
---

# Week 3

## Learning Objectives:

* Discover what network virtualization is and why it is used
* Identify various ways of implementing virtual networks
* Explain why Mininet is useful
* Practice how Mininet works
* Gain more experience using Mininet, setting up topologies, etc.

## Network virtualization

### definition: Abstraction of the physical network(物理网络的抽象)
* support multiple logical networks share a physical substrate(多个逻辑网络共享物理基础设施)
* container of network services

### Aspects of network virtualization(网络虚拟化的应用层面)

* Nodes: VM
* Links: Tunnels
* Storage

### Motivation & Goal

* Flexibility
* Manageability
* Scalability
* Security & Isolation
* Programmability
* Heterogeneity(异质性): 支持不同技术


### Virtual switch(虚拟交换技术)
Mechanism that taking Ethernet frames inside a VM and passing them to the physical interface, which networking VM together over L2 topology.(通过虚拟交换技术将虚拟机连接到二层网络)

Example:
* short-bridge in VINI: extension of Linux bridging(桥接)
* Open vSwitch: can be configured remotely with OpenFlow, json


## Application of virtual networking

* Experimental deployments
* Isolation on shared infrastructure
* Reuse of resource pool
* Dynamic scaling
* Easier management of logical resources

## Virtual network in Mininet

### Mininet
difiniton: a `virtual network environment` that can run on a single PC

*Mininet* VM architecture paradigm(review it in ppt/pdf):

1. lanuch mininet process(mn)
2. create subprocesses(/bin/bash) & network namespace in per vhost
3. create virtual ethernet(veth) pairs and assign to namespace(establish tunnel between virtual interface(VHost) and real interface(local).)
4. create OpenFlow switch to connect vhosts
5. create OpenFlow controller to control flow table entries in switch through OpenFlow protocol


