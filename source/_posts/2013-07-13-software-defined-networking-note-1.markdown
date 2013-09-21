---
layout: post
title: "Software Defined Networking - Note 1"
date: 2013-07-13 15:21
comments: true
categories: [SDN, OpenFlow]
---

> SDN (Software-Defined Networking) is an approach to computer networking which abstracts the distributed systems, the control plane and the data plane.
> 
>                                                         -- from Wikipedia

刚好coursera的SDN课程开在暑假，总算有个课是想上又有时间上了 = =

这里权当是做个笔记。因为本人英语渣，如果不作笔记的话基本上过了没多久就忘得一干二净了（虽然现在也差不多）。

实话说，课程比较抽象，ppt过了第一遍基本上就云里雾里了，很多地方都是知其然不知其所以然。虽然SDN是针对数据中心网络的技术，但是个人觉得网络硬件在这几年来确实没什么重大的革命（菜鸟我没听说过有咯），而现在各大互联网公司都在抢占用户上网入口，也就是路由器。如果SDN走出学术界，在工业界量产的话，那也算的上是网络硬件的革命了。难怪SDN会被称为Computer Network的X86。当然目前离大规模普及还是长路漫漫啊，具体面临的困难这里略去不表...（表不了啊）

以下内容参照 Coursera 的 [SDN open course](https://class.coursera.org/sdn-001/wiki/view?page=syllabus)

<!--more-->

# Week 1

## Active Networks

* definition: Networks where switches perform custom computations on packets.(交换机在数据包上执行自定义操作的一种网络)

* idea: Message carry procedures & data

  * Active routers coexist with legacy routers

  * Each programmable switch can perform additional processing

* Two different approaches

  * Capsules("integrated")(代码和数据整合到packet中)

  * Programmable Switches("discrete")(代码运行在路由器中，数据存储于packet，由header指定运行程序)

## Network Virtualization

* definition: Representation of one or more logical network topologies on the same infrastructure.(在同一个基础设施上表现出一个或多个网络拓扑结构)

* motivation: Separate logical network from physical infrastructure, potentially allow users to share the same underlying infrastructure for different purpose.(将逻辑的拓扑结构与物理基础设施分离以实现共享)

* benefits:

  * Sharing

  * Customizability




# Week 2


## Control plane(控制层)

* definition: Logic for controlling forwarding behavior(控制转发行为的逻辑，类似网络的大脑)

* e.g. routing protocol, network middlebox configuration(firewall,Load Balance)

## Data plane(数据层)

* definition: Forward traffic according to control plane logic(根据控制层的逻辑来转发流量)

* e.g. IP forwarding(即三层路由，根据IP地址进行转发), layer-2 switching(根据MAC地址进行转发)

## Reason for separate thr Control and Data plane

* Independent evolution and development

* Control from high-level software program:

> Once the control plane is separated from data plane, it's a lot easier to control the behavior of the network, because those switches are doing nothing more than just forwarding traffic, and all the smart of network are in the software control.

## Challenges from control/data plane separation

* scalability: routing decisions for many routers

* reliability: correct operation under failure

* consistency: ensuring consistency across multiple control replicas


## mininet(openflow tutorial)

**mininet** : A network emulation platform that has the ability to create a virtual OpenFlow network; controllers, switches, hosts, and links on a single real or virtual network.

### init

`sudo mn --topo single,3 --mac --switch ovsk --controller remote`

* sudo mn : This starts mininet. Mininet always requires sudo to run.
* --topo single,3 : This tells mininet to start using the topology of a ‘single’ switch and 3 hosts.
* --mac : This tells mininet to assign each host a sequential mac address, matching its IP address.
* --switch ovsk : This tells mininet that the switches are to be of the type ovsk, this is the type for Openflow,
* --controller remote : This tells mininet that each Openflow switch is to talk to a controller, which is located at a remote location.
* --link : This sets link parameters, e.g. bw(bandwidth), delay

Here's the topology:

![mininet](http://www.openflow.org/wk/images/c/c6/Three_switch_layout_simple.png)

### dpctl

`dpctl` is a utility that comes with the OpenFlow reference distribution and enables visibility and control over a single switch's flow table

<pre>
# forward packets coming at port 1 to port 2
dpctl add-flow tcp:127.0.0.1:6634 in_port=2,actions=output:3

# check the flow-table
dpctl dump-flows tcp:127.0.0.1:6634
</pre>

Note: the *idle_timeout* para means that the flow will expire after this many secs if there is no incoming traffic.

<pre>dpctl add-flow tcp:127.0.0.1:6634 in_port=2,idle_timeout=120,actions=output:3</pre>

### iperf

`iperf` is a command-line tool for checking speeds between two computers.

<pre>
# create an user space network
sudo mn --topo single,3 --controller remote --switch user
# Here we have used ‘user’ with --switch option instead of ‘ovsk’. This loads mininet with user-space switch.

# start a simple controller that acts as a learning switch without installing any flow-entries.
$ controller ptcp:

# test speed
mininet> iperf
# With the user-space switch, packets must cross from user-space to kernel-space and back on every hop, rather than staying in the kernel as they go through the switch. The user-space switch is easier to modify, but slower for simulation.

</pre>

## summary

* **OpenFlow Interface** : a standard open interface between the OpenFlow controller and the OpenFlow programmable devices (i.e., switches etc)
* **OpenFlow Controller** : sits *above* the OpenFlow interface. The OpenFlow reference distribution includes a controller that acts as an Ethernet learning switch in combination with an OpenFlow switch. You'll run it and look at messages being sent.
* **OpenFlow Switch** : sits *below* the OpenFlow interface. The OpenFlow reference distribution includes a user-space software switch. `Open vSwitch` is another software but *kernel-based* switch.


