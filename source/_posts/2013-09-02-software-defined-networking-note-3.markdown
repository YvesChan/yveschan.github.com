---
layout: post
title: "Software-Defined Networking - Note 3"
date: 2013-09-02 22:41
comments: true
categories: [SDN, OpenFlow]
---


# Week 4 - SDN Nuts and Bolts(螺母和螺栓) - Control Plane
===================

## Learing objectives:

* Review how an SDN controller can populate(填充) a switch's forwarding table.
* Determine how an SDN controller can be used to control multiple switches in a single network, and why to do so.
* Gain experience setting up a controller and basic switch environment.
* Understand the design tradeoffs between some of the more commonly used SDN/OpenFlow controllers. (一些广泛使用的控制器之间的设计抉择)
* Set up controller in Mininet that can perform basic operations,like switch-table population and firewalling.

## Control plane basics

### Openflow protocol specification(协议规范) (pic in PPT - P6)

* `OpenFlow controller` communicates with `switch` over Secure channel
  * OF protocol defines msg format
  * controller use control channel to update flow table
  * logic is executed at controller

* `Switch components` including:
  * flow tables: perform packet lookup, if matching then take `action`
    * actions: forward, drop, modify, enqueue
	* flow table format:
<pre>
 switch| MAC      |Eth |VLAN|IP               |TCP/UDP
 port  |[src][dst]|type|ID  |[src][dst][proto]|[sport][dport]|action
</pre>
  * secure channel

控制器与OF交换机之间通过SSL连接，使用OF协议进行通信，并在交换机上设置和更新流表。交换机只负责根据流表处理数据包，因此所有的逻辑都在控制器。每个数据包协议头部与所有流表(flow table)一一对比，如果匹配则执行相应的操作，如果所有都不匹配则发送到控制器(controller)。

* `dpctl` Control channel
  * switches default port: 6634
  * inspect, modify flow table

Even in the absence of a controller, we can use dpctl to talk to the switch.

<!-- more -->

### Openflow enhancements(v1.3)

* action set: 对每个包执行多个操作
* group: a list of action sets. 一个pipeline中包含多个table，每个match的table可以把更新数据包特定域(filed)的操作(action)添加到action set中，在到达出口(output port)前执行所有action set中的action.见P10


## SDN controller

## Using SDN controllers to customize control

### Hub and learning switch

* Hub
  * no forwarding info stored at switch
  * every input packet is flooded out all ports

Start a `POX controller` by running hub.py. When the POX controller receive a connection request, it send back the msg with modifying flow table entries(flood).

* learning switch(algorithm)

* Important concept: Listener

### Simple firewall(Important)

* performance: cache decisions at switch(把控制器的操作缓存在交换机以提高网络性能)
  * important to limit data traffic to controller
  * when controller decides to forward or drop, the flow table is modified.
  * decision is cache at switch until the flow table entry expired

当主机A要ping主机B时，第一个数据包的延时会比较大，因为交换机此时还没有flow table，需要把packet转发到controller，等controller根据rules作出转发决定，同时缓存该决定。每一段时间都会有一个icmp包的延时比较大，是因为flow table entry过期，要重复上述操作。

-----------

## Experiment

```sh
$ sudo dhclient eth1
$ sudo mn --topo single,3 --max --switch ovsk --controller remote     # create a simple topologic with 3 hosts & 1 OpenvSwitch
$ pox/pox.py log.level --DEBUG forwarding.hub     # start a pox's hub component
$ dpctl dump-flows tcp:127.0.0.1:6634    # check the flow table in switch
```

POX工作方式很简单，pox.py文件启动POX，并且后面接上需要载入的模块名字，可以一次接入多个模块，POX会执行模块中的launch()函数并将模块的状态设置为“up”，并且在模块后面可以给模块指定参数的。
### useful API

* connection.send()

* ofp_action_output(port = ..): a flow table action class.  It specifies a switch port that you wish to send the packet out of.

* ofp_match class

* ofp_packet_out()

* ofp_flow_mod(): OpenFlow modification msg. This instructs a switch to install a flow table entry.


### firewall behavior

* Hash table for storing K/V pairs (switch, src MAC)
* drop: if there is a firewall entry maps to "False" or no match entry
* forward: match a firewall entry that maps to "True"
