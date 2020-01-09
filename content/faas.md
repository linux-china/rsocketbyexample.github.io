+++
title = "FaaS - 函数计算"
+++

函数计算越来受到开发者的青睐，目前大多数函数服务都是通过HTTP REST API、gRPC，RPC等进行暴露的，同时函数旁边躺着Sidecar和Web Server这两个大胖子。
这种结构设计对对于传统的巨型应用来说问题不大，但是对于短小精悍的函数来说，这个完全是不划算的买卖，你需要为只有500行代码的函数付出10倍于函数本身的保护成本，而且加大地增加了系统的复杂性。

借助于RSocket，我们可以将FaaS的平台通讯层进行一些调整，结构图如下：

![FaaS Diagram](/images/integration/faas.png)

FaaS和RSocket在进行通讯协议整合后，一些历史遗留问题就不复存在啦:

* 无端口监听: 函数在主动连接到Broker并注册后，然后基于该连接完成相互间的通讯。对比传统的启动服务端口号然后对外提供服务，新的架构不需要Web Server、不需要HTTPS证书、不需要攻击防护等。
* 流量保护: Broker会作为代理人帮助函数完成安全验证、流量保护等。
* 基于消息/事件通讯: 很多人都说到，函数计算是基于消息/事件通知的，这个没有错，而RSocket协议就是基于异步消息
* 函数、服务间调用: 在Broker接入后，函数和其他服务或者函数之间的通讯就非常简单啦，函数不需要再引入复杂的SDK，只要向Broker发送调用请求就可以，然后Broker会负责服务路由和返回，这样函数负担也轻了很多

不能说RSocket协议和FaaS是绝配，但是确实让FaaS的架构设计更简单，去除不必要的累赘，让FaaS的成本更低。当然目前FaaS的发展并不是火箭式的速度，究其原因，我们不知道"是开发者不喜欢FaaS，还是昂贵复杂的FaaS让开发者怯步"。