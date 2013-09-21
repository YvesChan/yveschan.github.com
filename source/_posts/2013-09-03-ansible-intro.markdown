---
layout: post
title: "Ansible轻度使用指南"
date: 2013-09-03 21:35
comments: true
categories: [Dev-Ops, Ansible, configuration management]
published: false
---

> Ansible is a configuration management tool, deployment tool, and ad-hoc task execution tool all in one. If you are finding a light-weight automatic provisioning tool, then you should have a try,


正如ansible的作者Michael DeHaan所说，“My observation was that many shops end up using a configuration management tool (Puppet, Chef, cfengine), a separate deployment tool (Capistrano, Fabric) and yet another separate ad-hoc task execution tool (Func, pssh, etc) because one class of tool historically hasn't been good at all three jobs.” 也就说，在几年前并没有整合自动化配置，部署，点对点命令执行的多合一工具。同时，它具备一个轻量的工具所应该有的特点：

* 非C/S架构，无需后台服务进程和客户端支持
* 使用SSH进行连接和通信
* 目标机器无需安装额外依赖
* 支持多种安装方式, 包括ppa,pip,make install等，真正实现out of the box.
* 使用python开发,Linux原生支持,库依赖少(PyYAML Jinja2 paramiko)

在实际使用过程中，我觉得如果非要用一个形容词来形容ansible， 那就是轻量方便（想对于puppet或者chef来说）。对于我这种轻度洁癖症患者来说，这个真是福音。不止软件依赖少，而且基本上无需配置即可使用。如果非要说有什么不足，那可能就是细节未够完善，潜在的坑不少。


注意问题：
1. 在写playbooks的时候，所有的task尽量满足幂等性(idempotent)，简单的说就是执行多次和执行一次的效果是一样的，有关幂等的详细定义可以参考[Coolshell](http://coolshell.cn/articles/4787.html)的这篇文章。这样，如果任务中途失败或者有其他原因而中断，可以修改配置后再次执行。理论上，ansible原生的模块都是遵循幂等性的，因此在可选的前提下，我们应该尽量使用原生的功能模块而不是用shell等模块去运行ssh命令。
