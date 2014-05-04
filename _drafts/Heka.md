---
layout: post
title:  "Heka 学习笔记"
categories: logging
---

Heka 学习笔记

Build
===

Bin
===
./bin目录下所有的可执行文件的作用

**heka-logstreamer** : 当设置Logstreamer Input配置时，可以频繁使用这个命令进行配置有效性校验。具体请参考 https://hekad.readthedocs.org/en/latest/pluginconfig/

**hekad** : 真正的主角，守护进程。

##测试工具
https://hekad.readthedocs.org/en/latest/developing/testing.html

**heka-flood** : 压力测试用log生成工具		
**heka-inject** : heka-inject is a Heka client allowing for the injecting of arbitrary messages into the Heka pipeline. It is capable of generating a message of specified message variables with values. It allows for quickly testing plugins. Inject requires TcpInput with Protobufs encoder availability.

**heka-cat** : A command-line utility for counting, viewing, filtering, and extracting Heka protobuf logs.


##Sandbox(Lua)

**heka-sbmgrload**	 : Heka Sbmgrload is a test tool for starting/stopping a large number of sandboxes. The script and configuration are built into the tool and the filters will be named: CounterSandboxN where N is the instance number.https://hekad.readthedocs.org/en/latest/sandbox/index.html?highlight=sbmgr#heka-sbmgrload

**heka-sbmgr** : Heka Sbmgr is a tool for managing (starting/stopping) sandbox filters by generating the control messages defined above. https://hekad.readthedocs.org/en/latest/sandbox/index.html?highlight=sbmgr#heka-sbmgr


##Others
这两个命令没有找到想干说明

* mockgen
* protoc-gen-go





