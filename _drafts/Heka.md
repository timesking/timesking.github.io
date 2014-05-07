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

###测试工具
https://hekad.readthedocs.org/en/latest/developing/testing.html

**heka-flood** : 压力测试用log生成工具		
**heka-inject** : heka-inject is a Heka client allowing for the injecting of arbitrary messages into the Heka pipeline. It is capable of generating a message of specified message variables with values. It allows for quickly testing plugins. Inject requires TcpInput with Protobufs encoder availability.

**heka-cat** : A command-line utility for counting, viewing, filtering, and extracting Heka protobuf logs.


###Sandbox(Lua)

**heka-sbmgrload**	 : Heka Sbmgrload is a test tool for starting/stopping a large number of sandboxes. The script and configuration are built into the tool and the filters will be named: CounterSandboxN where N is the instance number.https://hekad.readthedocs.org/en/latest/sandbox/index.html?highlight=sbmgr#heka-sbmgrload

**heka-sbmgr** : Heka Sbmgr is a tool for managing (starting/stopping) sandbox filters by generating the control messages defined above. https://hekad.readthedocs.org/en/latest/sandbox/index.html?highlight=sbmgr#heka-sbmgr


###Others
这两个命令没有找到想干说明

* mockgen
* protoc-gen-go

Filters
===
###Common Filter Parameters


    message_matcher (string, optional):

        Boolean expression, when evaluated to true passes the message to the filter for processing. Defaults to matching nothing. See: Message Matcher Syntax

    message_signer (string, optional):

        The name of the message signer. If specified only messages with this signer are passed to the filter for processing.

    ticker_interval (uint, optional):

        Frequency (in seconds) that a timer event will be sent to the filter. Defaults to not sending timer events.



Sandbox
===


###dynamic loading

* SandboxFilters Heka运行时，仍然可以启动和停止
* SandboxDecoder 只能再Heka进行restart时进行启动和停止* 

###Lua Sandbox 必须实现的两个接口

int process_message()

    Called by Heka when a message is available to the sandbox. The instruction_limit configuration parameter is applied to this function call.
timer_event(ns)

    Called by Heka when the ticker_interval expires. The instruction_limit configuration parameter is applied to this function call. This function is only required in SandboxFilters (SandboxDecoders do not support timer events).

###Core API
所有核心API都开放了请[参考](https://github.com/mozilla-services/lua_sandbox/blob/master/docs/sandbox_api.md)

* require 返回一个table, For non user provided libraries the table is also globally registered with the library name. User provided libraries may implement there own semantics i.e. the grammar libraries return a table but do not globally register the table name (see the individual module documentation for the correct usage).
* output : To extend the function set exposed to Lua see lsb_add_function()

Lua 分为 Base 库和扩展库
扩展库必须显示require才能使用， 目前扩展库包括

* [circular_buffer](https://github.com/mozilla-services/lua_sandbox/blob/master/docs/circular_buffer.md)
* cjson
* lpeg
* math
* os
* string
* table

Core APIs

* read_config(variableName)
* read_message(variableName, fieldIndex, arrayIndex)
* [DECODER]write_message(variableName, value, representation, fieldIndex, arrayIndex)
* read_next_field()
* inject
  * inject_message(payload_type, payload_name)
  * inject_message(circular_buffer, payload_name)
  * inject_message(message_table)


###SandboxManagerFilter
The SandboxManagerFilter provides dynamic control (start/stop) of sandbox filters in a secure manner without stopping the Heka daemon.



    Common Filter Parameters

    working_directory (string):

        The directory where the filter configurations, code, and states are preserved. The directory can be unique or shared between sandbox managers since the filter names are unique per manager. Defaults to a directory in ${BASE_DIR}/sbxmgrs with a name generated from the plugin name.

    module_directory (string):

        The directory where ‘require’ will attempt to load the external Lua modules from. Defaults to ${SHARE_DIR}/lua_modules.

    max_filters (uint):

        The maximum number of filters this manager can run.



    memory_limit (uint):

        The number of bytes managed sandboxes are allowed to consume before being terminated (max 8MiB, default max).

    instruction_limit (uint):

        The number of instructions managed sandboxes are allowed the execute during the process_message/timer_event functions before being terminated (max 1M, default max).

    output_limit (uint):

        The number of bytes managed sandbox output buffers can hold before before being terminated (max 63KiB, default max). Anything less than 64B is set to 64B.


####Control Message
Starting a SandboxFilter

    Type: “heka.control.sandbox”
    Payload: sandbox code
    Fields[action]: “load”
    Fields[config]: the TOML configuration for the SandboxFilter sandboxfilter_settings

Stopping a SandboxFilter

    Type: “heka.control.sandbox”
    Fields[action]: “unload”
    Fields[name]: The SandboxFilter name specified in the configuration

#####heka-sbmgr

#####heka-sbmgrload

###How to use Sandbox
https://hekad.readthedocs.org/en/latest/sandbox/index.html#tutorial-how-to-use-the-dynamic-sandboxes

* https://hekad.readthedocs.org/en/latest/sandbox/json_payload_transform.html#json-payload-transform
* https://hekad.readthedocs.org/en/latest/sandbox/graph_annotation.html#graph-annotation

TODO
===
http://blog.csdn.net/akof1314/article/details/8049649

http://www.inf.puc-rio.br/~roberto/lpeg/lpeg.html
需
要复习Lua脚本知识， 需要学习Lpeg库



