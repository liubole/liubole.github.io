
---
title: rabbitmq分享
tags: [rabbitmq]
categories: [中间件,rabbitmq]
toc: false
date: 2016-11-09 21:58:09
---


消息队列（MessageQueue）：
简化业务流程
异步处理消息
减少组件耦合
消减数据峰值

场景1：
    支付成功以后，给用户发送优惠券or红包；
场景2：
    用户注册成功后发送通知邮件or短信；
场景3：
    活动里面的站内信发送；
......
总结起来就是，把单个业务简洁化，通过消息系统把不相关的业务连接起来，实现更复杂的业务链，降低整个系统的复杂度。

安装
省略。。。

使用
四个重要概念：虚拟主机（vhost）、交换器（exchange）、队列（queue）、绑定（binding）

先说交换器和队列，附带消息的说明：

队列属性（部分）：
    名称(queue)：
    持久化(durable)：服务重启后是否要恢复
    自动删除(auto-delete)：没有消费者时自动删除
    其他声明：
        消息存在时间(x-message-ttl)：
        队列存在时间(x-expires)：
交换器属性（部分）：
    名称(exchange)：
    类型(type)：fanout/direct/topic/headers
    持久化(durable)：服务重启后是否要恢复
    自动删除(auto-delete)：没有队列使用交换器时自动删除
消息（部分）：
    消息体(body)
    属性（properties）：
        投递模式(delivery_mode)：可设置为持久化
绑定（exchange-queue）：
    和交换器、队列不一样，绑定是一种规则而非实体。

*持久化和自动删除并不冲突
*队列、交换器的属性设置好后就不能修改，除非删掉重建

在我们的项目中是如何使用RabbitMQ的：
    
 1. 持久化：要达到一般人理解的持久保存消息，需要设置三个地方
        1.将交换机设置成durable；
        2.将队列设置成durable；
        3.将消息的delivery_mode设置成2；
        注意两点：1.缺少任何一个，都无法达成真正持久化消息的目的；
            2.持久化会消耗额外的性能；

 2. 自动删除：消息&队列生存时间、“死信队列”这些都是自己设置的。
        消息在队列里存在时间是1分钟，队列本身存在时间是30分钟，过期的消息会被移出到“死信队列”，死信队列里的消息存在7天，死信队列本身存在10天；
    消息消费后ack/nack机制(acknowledgement)：
        如果能保证消息已经被正确消费了，你可以通过ack方法告诉rabbitmq，rabbitmq会删除这条消息；
        反之，你可以调用nack告诉RabbitMQ消费失败（rabbitmq会将消息立马移到“死信队列”），或者什么都不做（等待消息过期被移动到“死信队列”）。
        
 3. 发布消息后confirm机制：暂时关闭了这个功能。
        发布消息后，发送者可以通过异步的回调知道消息是否发送成功。

代码：
use CI123\Shop\Tool\RabbitMq\Publisher;
Publisher::publish('coupons.user_coupon.add', json_encode($coupons));

use Api\Tool\RabbitMq\Subscriber;
Subscriber::subscribe('shop_com.add.trade_deliver', function ($msgObj) {
    $message = $msgObj->body;
    Subscriber::ack($msgObj);
});
    
其他，关于RabbitMQ的知识很多也比较琐碎，建议大家直接提问。





