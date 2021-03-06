- 设计即时通讯系统(ACE, JiuZhang, groking_system_design_interview)

- design要给出一个workable solution

- ### design whatsapp, design facebook messenger, instagram， design wechat design facebook live comments design design slack


- 聊天系统的核心： real time service
- online status: pull vs push

https://www.youtube.com/watch?v=zKPNUMkwOJE


https://blog.usejournal.com/whatsapp-system-design-and-chat-messaging-architecture-part-1-29fb4f0d14af

https://medium.com/codingurukul/whatsapp-engineering-inside-1-1ef4845ff784
https://medium.com/codingurukul/whatsapp-engineering-inside-2-bdd1ec354748


1. 如何分析数据流？  facebook messenger vs whatsapp

1. whatsapp支持单个设备，不支持聊天历史记录,一旦接收方接收了，就从服务器上删除， end to end encryption is turned on 

1. facebook messanger:支持多个设备，支持聊天记录,保留所有聊天记录在服务器上直到你删除它, end to end encryption is turned on by option

1. whatsapp多个数据流：面试过不过，取决于能不能分析出4条数据流

1. highlight:  隐私设计(服务器端不存储消息历史，传输过程加密)，数据流


### features / topics
 - one to one chatting
 - online/send/read
 - sending pictures or other files
 - database
 - security


# design whatsapp

### requirments
![20210705095305](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705095305.png)


![20210705100245](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705100245.png)


- 使用push还是pop? push,为什么？ 保证极低的延迟

- 服务器端和客户端使用什么协议去交流？
    - 好几种选择： http restful api
    - 双向交流需要websocket


- 客户端和服务器协议：
  ![20210705101508](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705101508.png)



- **http** long polling与websocket什么区别？



### estimation
- whatsapp的系统瓶颈在哪里？ 
    - ongoing connection消耗内存


- 使用websocket的服务，ongoing  connection都是瓶颈

- websocket单机最多能维持10M connecton,不会受到tcp ports的限制（65k）

- 存储资源怎么计算？ 

![20210705112550](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705112550.png)

服务资源相当于是没有放过peak value的qps(request, response pattern下面做request)

为什么不叫qps,叫message delivery rate:

## data flow
- data flow的设计相当于是test case, data flow设计清楚了先，再system design, 相当于Test driven development


### 一对一聊天 消息状态的四条核心信息流： 
- 发送方和接受方都在线

- 发送方在线，接收方不在线

### One to One Chat and Sent + Delivered + Read receipts

![20210705130718](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705130718.png)

- sender, receipent的websocket server不一样，messing service不一样，同时要考虑消息顺序问题


![20210705130730](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705130730.png)

recepient B 上线之后去poll所有的消息
双方都在线的时候使用pull去获得消息


![20210705130745](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705130745.png)


![20210705135640](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705135640.png)
### whatsapp的流量模式是什么？
流量模式：大量的request的总体特征是什么

移动网路不稳定对系统设计图有什么影响？

如何保证消息顺序？  如何两个人同时发消息，同时点发送，怎么保证消息的顺序的正确性？ 
1. queue  客户端排序，客户端机器的本地时间
   

队列怎么存消息？ 按照收件人还是按照发件人shard,按照收件人sharding,队列的目的保证收件人看消息按照一定的顺序

![20210705133408](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705133408.png)


为什么write back cache这里合适？因为手机网路不稳定，减少db write,用户掉线重新上线后可以更快的access消息
![20210705133323](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705133323.png)


![20210705134727](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705134727.png)

## 隐私 （传输消息如何加密）
下图展示的是不对称加密
![20210705134855](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705134855.png)



## high level design

![20210705140404](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705140404.png)

## storage

### db design
![20210705144140](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705144140.png)

![20210705144156](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705144156.png)


### api design

send message

![20210705144609](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705144609.png)

### 扩展性


## 监控警报
![20210705144814](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705144814.png)


# design facebook messenger
## facebook messenger的考察重点是存储：


![20210705150002](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705150002.png)
![20210705145805](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705145805.png)

![20210705150030](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705150030.png)



### design wechat

![20210705203108](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705203108.png)

![20210705204705](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705204705.png)

wechat是点对点的吗？ 看了之后会在服务器上保留一个月

微信语音是p2p的

telegram是点对点加密  whatsapp也是点对点的

### 服务
messaging service 负责信息存取
realtime service  负责信息推送

![20210705205831](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705205831.png)

![20210705205850](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705205850.png)


![20210705210156](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705210156.png)

![20210705210259](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705210259.png)

![20210705210325](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705210325.png)

![20210705210422](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705210422.png)

![20210705210435](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705210435.png)


问题：有些thread信息是私有的
怎么解决？  

![20210705211124](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705211124.png)




![20210705224410](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705224410.png)


把updated_at挪到user thread table支持按更新时间排序，如果放在另一种表里就会效率非常的低

![20210705211521](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705211521.png)

![20210705211532](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705211532.png)

工程中如何避免join:把常用的thread cache起来

![20210705212913](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705212913.png)


![20210705224219](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210705224219.png)


![20210706080820](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706080820.png)

![20210706083327](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706083327.png)


sql中一张表建两个index, 在nosql里怎么做，建两张表


![20210706084514](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706084514.png)

![20210706084744](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706084744.png)

### sharding的原则是根据查询的需求来的

![20210706092722](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706092722.png)

![20210706093234](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706093234.png)

![20210706093402](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706093402.png)

![20210706100154](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706100154.png)

![20210706103022](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706103022.png)


### 使用channel service 优化群聊

![20210706104013](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706104013.png)

![20210706104503](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706104503.png)

![20210706104643](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706104643.png)

考点：
数据流功能设计
数据库设计
api设计

![20210706120631](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706120631.png)

### 多机登录
![20210706121318](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706121318.png)

![20210706122703](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706122703.png)

![20210706124055](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706124055.png)

![20210706124330](https://raw.githubusercontent.com/corykingsf/hack-system-design-pixel/main/pictures/20210706124330.png)