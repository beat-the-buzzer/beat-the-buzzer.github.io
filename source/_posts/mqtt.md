---
title: MQTT介绍：简单的实时通讯功能（web端）
date: 2022-02-06
tags: [通信]
categories: 
- 通信
---

MQTT是一个轻量级的发布/订阅消息传输协议。MQTT协议是轻量、简单、开放和易于实现的，这些特点使它适用范围非常广泛。

如何接入MQTT？

一般我们通过`paho.mqtt`来接入MQTT。

服务端定义消息的结构：

```java
message Message {
	required uint64 message_id = 1;
	required uint64 sender_id = 2;
	required string content = 3;
	required uint64 create_time = 4;
	required uint64 receiver_id = 5;
}
```

我们可以把服务端定义的消息结构编译成JS，这样就能够在web端解析消息了。

如何编译？[](https://github.com/protocolbuffers/protobuf/releases)

```shell
protoc --js_out=import_style=commonjs,binary:./ messages.proto
```

```json
message ServiceMessage {
	required uint64 message_id = 1 [jstype = JS_STRING]; // 解决js丢失大数字精度问题
	required uint64 user_id = 2;
	required ServiceType service_type = 3;
	required MessageType message_type = 4;
	required string content = 5;
	required uint64 create_date = 6;
	required ServiceMessageDirection direction = 7;
	optional uint64 agent_id = 8;
	optional uint32 post_proc = 9;
	optional uint32 to_reply = 10;
	optional string extend = 11;
}
```

[](https://github.com/protocolbuffers/protobuf/releases/download/v21.8/protoc-21.8-linux-x86_64.zip)

注意长数字的转换，会有精度丢失的问题。因为前端的大数字会丢失精度，所以需要在编译的时候将其转成字符串。

引用 paho_mqtt.js：[](https://github.com/eclipse/paho.mqtt.javascript) 引用 paho_mqtt.js

```js
import sendMessage from './msg_pb.js'
import Paho from './paho-mqtt.js'

// MQTT的连接
this.client = new Paho.MQTT.Client(HOSTNAME, PORT, CLIENTID)
this.client.onConnectionLost = this.onConnectionLost // 注册连接断开处理事件
this.client.onMessageArrived = this.onMessageArrived // 注册消息接收处理事件
this.client.onMessageDelivered = this.onMessageDelivered // 发送消息成功之后的回调函数

onConnectionLost(msg) {
	// 重连的逻辑
}

onMessageArrived(msg) {
	// 消息接收的逻辑
	let message = sendMessage.BaseUserUnreadStatics.deserializeBinary(msg.payloadBytes)
	let protoBuf = message.toObject() // 解析出来的消息
}

onMessageDelivered(msg) {
	// 消息发送成功之后
}

// 发送消息
sendMsg() {
	let message = new sendMessage.Message()
	message.setMessageId('xxx')
	message.setSenderId('xxx')
	message.setContent('xxx')
	message.setCreateTime('xxx')
	message.setReceiverId('xxx')
	
	let bytes = message.serializeBinary() // 序列化
	let msgSend = new Paho.MQTT.Message((bytes))
	msgSend.topic = 'xx'
	this.client.send(msgSend) 
}
```

## 实时通讯遇到的问题（技术+业务）

1. 消息发送和接收的时间有误差，导致消息的顺序不正确。

2. 业务：我们需要实时记录未回复的消息。无法准确评判未回复的通讯对象：
 - 按照服务端接收消息的顺序
 - 按照客户端接收消息的顺序
	 
3. HTTP请求和TCP的消息有一个时间差。
 - 12:00:00的时候，调用接口获取聊天记录
 - 12:00:01的时候，对方发送了一条消息
 - 12:00:02的时候，获取聊天记录的接口回来了

此时对方在12:00:01发送的消息就不在消息列表中。

4. 消息回执的处理

Qos级别设置为1或2的时候，服务端在接受到消息之后，会发送一条回执消息给用户，用于标识消息已成功发送。我们在mqtt源码里，能找到对应的对消息回执的处理，一般服务端会把消息的id回填过来，需要我们去解析数据：

```js
case PUBACK:
	// ...
```

根据服务端定义的消息的位数（一般是16位或者64位），调用不同的解析方法：

```js
// 解析16位数据
function readUint16(buffer, offset) {
	return 256 * buffer[offset] + buffer[offset + 1]
}

// 解析64位数据：注意如果使用number类型，数据会越界，使用bigInt再将其转成字符串
function readUint64(buffer, offset) {
	const uint64 = buffer.slice(offset, offset + 8)
	const uint64Array = new Uint8Array(uint64)
	const hexString = Array.from(uint64Array, (byte) => ('0' + byte.toString(16)).slice(-2)).join('')
	const uint64Str = BigInt(`0x${hexString}`).toString()
	return uint64Str
}
```

开发建议：

1. 将聊天记录记录在本地，下次进入页面无需调用聊天记录接口（类似QQ、微信的做法）
2. 实时记录未回复的消息，需要给消息新增回执，即对方已经收到消息的标志。类似于钉钉的消息是否已读的功能。


