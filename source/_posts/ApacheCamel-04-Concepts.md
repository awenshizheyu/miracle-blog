---
title: 04-ApacheCamel系列文章-重要概念
categories: 
- ApacheCamel系列文章
---
注：本节内容因为属于概念的问题，基本都是来自网上摘抄，非本人原创，如有侵权，请联系我删除。

## Camel Context
camel的运行容器，管理所有的camel路由。类似于spring中的context。

## Route
顾名思义，Route，就是路由，它定义了Message如何在一个系统中传输的真实路径或者通道。路由引擎自身并不暴露给开发者，但是开发者可以自己定义路由，并且需要信任引擎可以完成复杂的传输工作。每个路由都有一个唯一的标识符，用来记录日志、调试、监控，以及启动或者停止路由。
路由也有一个输入的Message，因此他们也有效的链接到一个输入端点。路由定义了一种领域特有的语言（DSL）。Camel提供了java、scala和基于XM的Route-DSL。
示例路由：
```
//simple route.
from("file:data/inbox").to("jms:queue:order")
```
路由可以使用过滤器、多播、接收列表、并行处理来定义，从而变得非常灵活。由于这篇文章只是简单的介绍Camel，我这里只给出一个注释的例子。这个使用了“direct:”架构，他提供了当消息生产者发出消息后直接的、同步的调用。
```
//Every 10 seconds timer sends an Exchange to direct:prepare
from("timer://foo?fixedRate=true&period=10000").to("direct:prepare");
// Onother Routes can begin from "direct:prepare"
// This now depends on timer, logging and putting a message to the queue.
from(direct:prepare).to("log:com.mycompany.order?level=DEBUG").to("jms:queue:order?jmsMessageType=Text");
```

## Endpoint
是Camel中的一个基本概念，Endpoint作为Camel系统中一个通道的端点，可以发送或者接受消息。在Camel中Endpoint使用URI来配置。在运行时Camel通过URI来查找端点。端点的功能强大、全面而且又可维护。
个人理解为一个路由（流程）的每一个环节，这个endpoint会定义在该环节中做什么操作

## Component
Component是一些Endpoints URI的集合。他们通过连接码来链接（例如file:），而且作为一个endpoint的工厂。现在Camel中又超过80个Component，可扩展。 
个人理解是对Endpoint的一个封装，因为每一类Endpoint都需要一个uri作为入口。那么就需要对这个Endpoint的producer和consumer进行封装。

## Exchange
非常重要的一个概念，数据的交换就靠他了，一个消息之间通信的抽象的会话。主要包括: 

* ExchangeId（唯一标识） 
* MEP（一种模式，有InOnly、OutOnly等） 
* Exception（路由过程中的异常） 
* Properties（可以进行传递的属性，是键值对） 
* Message（InMessage和OutMessage）。

## Message
Camel中一个基本的包含数据和路由的实体，Messages包含了 

* 唯一的识别（Unique Identifier）–java.lang.String类型 
* 头信息（Headers）–会提供一些内容的提示，头信息被组织成名值对的形式，string–>Object 
* 内容（body）是一个Object类型的对象，这就意味着，你要确保接收器能够理解消息的内容。当消息发送器和接收器使用不同的内容格式的时候，你可以使用Camel的数据转换机制将其转换为一个特定的格式。在许多情况下预先定义类型可以被自动转换。 
* 错误标记(fault flag)使用来标记正常或者错误的标记，通常由一些标准类定义，例如（WSDL）

## Processor
是一个消息接受者和消息通信的处理器。
当然，Processor是Route的一个元素，可用来消息格式转换或者其他的一些变换。 
个人理解就是对exchange做处理的一个环节，不过是将它单独拿出来而已。

## Producer
生产者是Camel抽象，是指能够创建消息并将消息发送到端点的实体。
当需要将消息发送到端点时，生产者将创建一个交换并使用与该特定端点兼容的数据来填充它。
例如，FileProducer会将消息正文写入文件。 另一方面，JmsProducer会将Camel消息映射到javax.jms.Message，然后将其发送到JMS目标。 这是Camel的一个重要特征，因为它隐藏了与特定运输交互的复杂性。 所有你需要做的就是把消息路由到一个端点，生产者做繁重的工作

## Consumer
消费者是接收生产者产生的消息的服务，将其包装在交换中并发送给他们进行处理。 消费者是Camel交易的来源。
为了创建新的交换，消费者将使用包装所消耗的有效载荷的端点。 然后使用处理器来使用路由引擎启动Camel交换机的路由。
在Camel中有两种消费者：事件驱动的消费者和轮询的消费者。
这些消费者之间的差异是重要的，因为他们帮助解决不同的问题。一个事件驱动的消费者等待空闲，直到消息到达，然后唤醒并消费该消息。最熟悉的消费者可能是事件驱动的消费者，这种消费者大多与客户端 - 服务器体系结构和Web服务相关联。 它在EIP世界中也被称为异步接收器。 
事件驱动的使用者在特定的消息通道（通常是TCP / IP端口或JMS队列）上侦听，并等待客户端向其发送消息。 当消息到达时，消费者醒来并把消息处理。

## Introduction to Java DSL
对于许多人来说，要实现一个“domain-specific language” （面向领域的语言）涉及到了一个能够处理这个特定领域语言的关键字以及语法的编译器或者是解释器。对于Camel来说，它没有这么做。在Camel文档中一直都在使用的“Java DSL”而不是 “DSL” ，其目的就是想避免混淆这两个概念。Camel中的“Java DSL”是一个可以像DSL一样被使用的类库，除此之外它还使用了大量Java的语义。你可以看一下下面的例子，在例子下面的备注里， 解释了这个例子的中所用的组件。
```
Example of Camel's "Java DSL"
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("queue:a").filter(header("foo").isEqualTo("bar")).to("queue:b");
        from("queue:c").choice()
                .when(header("foo").isEqualTo("bar")).to("queue:d")
                .when(header("foo").isEqualTo("cheese")).to("queue:e")
                .otherwise().to("queue:f");
    }
};
CamelContext myCamelContext = new DefaultCamelContext();
myCamelContext.addRoutes(builder);
```
上面例子的第一行创建一个一个RouteBuilder的匿名类的实例，这个匿名类需要实现 configure（）方法。
camelContext.addRoutes(RouterBuilder builder) 方法中调用了builder.setContext(this)方法，这样RouteBuilder对象就获得了与之对应的CamelContext的，然后调用builder.configure()方法。在configure方法中，可以调用例如 from()， filter(), choice(), when(),isEqualTo(), otherwise()以及to() 方法。
RouteBuilder.from(String uri) 方法会调用与之对应的CamelContext的getEndpoint(uri)方法来获得指定的Endpoint，并用一个FromBuilder包装这个Endpoint。这样 FromBuilder.filter(Predicate predicate) 方法就会创建一个在header("foo").isEqualTo("bar")这个表达式基础创建的Predicate（所谓的条件）创建一个FilterProcessor对象。就这样， 通过定义这些操作我们逐渐构建出了一个Route对象（使用RouterBuilder进行包装的）并且将这个Route对象添加进了与RouteBuilder所关联的CamelContext中。

END