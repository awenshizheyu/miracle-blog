---
title: 01 - Apache Camel 系列文章 -- 简介
---

## 背景
工作中经常用到Apache Camel（下文用Camel代替），每次用都要找资料，没有积累，加上Camel因为属于企业级集成组件，网上的资料很少、不全面，官方提供的文档又全都是英文，每次都很纠结，所以，决定写一套文档，记录Camel的相关问题。

注：本节内容因为属于概念的问题，基本都是来自网上摘抄，非本人原创，如有侵权，请联系我删除。

## 什么是Camel？
Apache Camel 是一个非常强大的基于规则的路由以及媒介引擎，该引擎提供了一个基于POJO的 企业应用模式(Enterprise Integration Patterns)的实现，你可以采用其异常强大且十分易用的API (可以说是一种Java的领域定义语言 Domain Specific Language)来配置其路由或者中介的规则。
通过这种领域定义语言，你可以在你的IDE中用简单的Java Code就可以写出一个类型安全并具有一定智能的规则描述文件。这与那种复杂的XML配置相比极大简化了规则定义开发。 当然Apache Camel也提供了一个对Spring 配置文件的支持。 Apache Camel 采用URI来描述各种组件，这样你可以很方便地与各种传输或者消息模块进行交互，其中包含的模块有 HTTP, ActiveMQ, JMS, JBI, SCA, MINA or CXF Bus API。 这些模块是采用可插拔的方式进行工作的。Apache Camel的核心十分小巧你可以很容易地将其集成在各种Java应用中。 Aapche Camel支持DSL(domain-specific languages)特定领域语言。
以下给出官方给出的Camel的简介（英文版）。
What is Camel?
At the core of the Camel framework is a routing engine, or more precisely a routingengine builder. It allows you to define your own routing rules, decide from which sources to accept messages, and determine how to process and send those messages to other destinations. Camel uses an integration language that allows you to define complex routing rules, akin to business processes.
One of the fundamental principles of Camel is that it makes no assumptions about the type of data you need to process. This is an important point, because it gives you, the developer, an opportunity to integrate any kind of system, without the need to convert your data to a canonical format.
Camel offers higher-level abstractions that allow you to interact with various systems using the same API regardless of the protocol or data type the systems are using. Components in Camel provide specific implementations of the API that target different protocols and data types. Out of the box, Camel comes with support for over 80 protocols and data types. Its extensible and modular architecture allows you to implement and seamlessly plug in support for your own protocols, proprietary or not. These architectural choices eliminate the need for unnecessary conversions and make Camel not only faster but also very lean. As a result, it’s suitable for embedding into other projects that require Camel’s rich processing capabilities. Other open source projects, such as Apache ServiceMix and ActiveMQ, already use Camel as a way to carry out enterprise integration.
We should also mention what Camel isn’t. Camel isn’t an enterprise service bus(ESB), although some call Camel a lightweight ESB because of its support for routing, transformation, monitoring, orchestration, and so forth. Camel doesn’t have a container or a reliable message bus, but it can be deployed in one, such as OpenESB or the previously mentioned ServiceMix. For that reason, we prefer to call Camel an integration framework rather than an ESB.
**以下是对上文的翻译：**
什么是Camel？
Camel框架的核心是一个路由引擎，或者更确切地说是一个路由引擎构建器。它允许您定义自己的路由规则，决定从哪个源接收消息，并确定如何处理这些消息并将其发送到其他目标。 Camel使用集成语言，允许您定义复杂的路由规则，类似于业务流程。
Camel的基本原则之一是它不会假设你需要处理的数据类型。这是一个重要的观点，因为它使开发人员有机会整合任何类型的系统，而不需要将数据转换为规范的格式。
Camel提供更高层次的抽象，使您可以使用相同的API与各种系统进行交互，而不管系统使用的协议或数据类型如何。 Camel中的组件提供了针对不同协议和数据类型的API的特定实现。开箱即用，Camel支持80多种协议和数据类型。其可扩展的模块化体系结构允许您实现并无缝插入支持您自己的协议，专有或非专有协议。这些架构选择消除了不必要的转换需求，使Camel不仅更快，而且非常精益。因此，它适合嵌入到其他需要Camel丰富处理能力的项目中。
其他开源项目（如Apache ServiceMix和ActiveMQ）已经使用Camel作为实现企业集成的一种方式。
我们还应该提到Camel不是什么。Camel不是一个企业服务总线（ESB），虽然有些人称Camel是一个轻量级的ESB，因为它支持路由，转换，监视，编排等等。Camel没有容器或可靠的消息总线，但可以部署在一个，如OpenESB或前面提到的ServiceMix。出于这个原因，我们宁愿给Camel一个集成框架，而不是一个ESB。

END