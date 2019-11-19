---
title: 03 - Apache Camel 系列文章 -- 使用场景
categories: 
- Apache Camel 系列文章
---
注：本节内容因为属于概念的问题，基本都是来自网上摘抄，非本人原创，如有侵权，请联系我删除。

下面列列举一些Camel的使用场景：

## 1. 消息汇聚
比如你有来自不同服务器的消息，有ActiveMQ，RabbitMQ，WebService等，你想把它们都存储到日志文件中，那么可以定义如下规则。
```
new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("amqp:queue:incoming").to("log:com.mycompany.log?level=DEBUG");
        from("rabbitmq://localhost/A/routingKey=B").to("log:com.mycompany.log?level=DEBUG");
        from("jetty:http://localhost:8080/myapp/myservice").to("log:com.mycompany.log?level=DEBUG");
    }
}
```
from表示从这个endpoing取消息，to表示将消息发往这个endpoint，endpoint是消息地址，包含协议类型以及url。 

## 2. 消息分发
分为两种，顺序分发和并行分发。
顺序分发时，消息会先到到第一个endpoing，第一个endpoint处理完成后，再分发到下下个endpoint。如果第一个endpoing处理出现故障，那么消息不会被传到第二个endpoint。比如有如下规则:
```
from("amqp:queue:order").to("uri:validateBean", "uri:handleBean", "uri:emailBean");
```
这个规则是从order队列中取订单信息，然后依次验证订单，处理订单，并发送邮件通知用户。任何一个步骤出错，下一个步骤将不回执行。
并行分发是将得到的消息同时发送到不同的endpoint，没有先后顺序之分，各个endpoint处理消息也是独立的。如果将以上路由改成:
```
from("amqp:queue:order").multicast().to("uri:validateBean", "uri:handleBean", "uri:emailBean");
```
那么消息就会同时发到to所对应的endpoint。

## 3. 消息转换
比如想将xml数据转换成json数据，可以使用如下规则。
```
from("amqp:queue:order").process(new XmlToJsonProcessor()).to("bean:orderHandler");
```
其中XmlToJsonProcessor是自定义的类，继承org.apache.camel.Processor，用于将xml数据转换成json。

## 4. 规则引擎
你可以使用Spring Xml, Groovy这类DSL来定义route，这样无需修改代码，就能达到修改业务逻辑的目的。
例如上边的规则，
```
from("amqp:queue:order").to("uri:validateBean", "uri:handleBean", "uri:emailBean");
```
使用Spring Xml定义如下：
 
```
<route>
        <from uri="amqp:queue:order"/>
        <multicast>
            <to uri="uri:validateBean"/>
            <to uri="uri:handleBean"/>
            <to uri="uri:emailBean"/>
        </multicast>
</route>
```
 
如果需要在处理完订单后添加日志，可以改称如下规则
 
```
<route>
        <from uri="amqp:queue:order"/>
        <multicast>
            <to uri="uri:validateBean"/>
            <to uri="uri:handleBean"/>
            <to uri="log:com.mycompany.log?level=INFO"/>
            <to uri="uri:emailBean"/>
        </multicast>
</route>
```
另外camel提供了大量的内置Processor，用于逻辑运算，过滤等，这样更加容易定移除灵活的route，例如：
```
from("amqp:queue:order")
    .filter(header("foo").isEqualTo("bar"))
    .choice()
    .when(xpath("/person/city = &#39;London&#39;"))
        .to("file:target/messages/uk")
    .otherwise()
        .to("file:target/messages/others");
```
这条规则先对订单进行过滤，只处理header中foo的值为bar的订单，然后根据用户的城市进行将订单传给不同的endpoint。
Apache Camel的应用场景有很多，这里只是大致列举了几种。

END