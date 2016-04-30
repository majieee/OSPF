# OSPF
summay for ospf study

DEF:

open shortest path first --- 开放式最短路径优先， 链路状态路由协议

协议号： 89

管理距离： 110

COST:

10^8 / INTERFACE BANDWIDTH(bit/s)

METRIC:

为多个OSPF接口COST之和，此处接口均指OSPF出接口

Router-ID:

优先级如下：

1： 手动指定

2： LOOPBACK INTERFACE IP

3： PHYSICAL INTERFACE IP

LINK :  运行 OSPF 进程的接口

LINK STATE: 接口的IP ,MASK,COST,NETWORK TYPE 等等

OSPF区域： 

1 所有接口在同一个区域，该路由器称为 internal router (IR)

2 接口位于不同的区域，该路由器称为 area border router (ABR)

3 接口将外部路由重分布进入OSPF，该路由器称为 autonomous system border router(ASBR)

OSPF NEIGHBOR :

建立邻居的条件，AREA ID需要相同，HELLO/DEAD INTERVAL 相同，AUTHENTICATION PWD相同

OSPF ADJACENCY :

交换LSA的NEIGHBOR才可以称之为ADJACENCY，比如DR-OTHER之间就不会交换LSA，但他们是邻居，会交换HELLO PACKETS

DR/BDR：

选举DR/BDR的原则： 首先看接口优先级，IP OSPF PRIORITY(0~255)，默认为1 ，数值越大优先级越高，为0 代表不参与选举，然后看ROUTER-ID的大小

选举有时间限制，不能超过wait timer == 4 hello interval ,超时之后不再重新举行选举

OSPF数据包交换过程：

HELLO PACKETS -----> DBD (DATABASE DESCRIPTION PACKETES) ----->LSR (LINK-STATE REQUEST) ---->LSU (LINK-STATE UPDATE)
----->LSACK (LINK-STATE ACKNOWLEDGE)

OSPF 启动过程：

1 DOWN ---- 此状态尚未收到HELLO PACKTES,但可以发送HELLO PACKTES

2 INIT ---- 此状态已经收到HELLO PACKTES，但对方尚未将自己列为邻居

3 TWO-WAY---此状态双方已经交换了HELLO PACKETS，也互相列为邻居，DR/BDR也已经选举完毕，对于DR-OTHER ，不再进入下面的过程。

4 EXSTART---对于DR/BDR,在交换DBD之前，需要确定主从路由器，根据ROUTER-ID大小确定DBD的发送先后顺序

5 EXCHANGE--主从确定之后，开始交换DBD

6 LOADING --DBD收到后，进入LSR,LSU,LSACK过程

7 FULL -----收到所有对方的LSU，并放入自己的LSDB之后，进入LSDB收敛状态，实现数据库同步，路由表仍然处于计算状态

8 ATTEMPT ---对于组播环境没有此过程，对于非广播环境需要指定邻居地址，发送单播HELLO PACKETS

OSPF 网络类型（Network Type）： 实际上就是OSPF接口所处的数据链路层类型（LAYER 2 ）

1 广播 ，非广播 两种类型，广播都是必须选择DR/BDR，但是广播建立邻居是自动的（通过组播包
224.0.0.5），非广播必须手动指定对端邻居的地址。

2 点到点，点到多点类型，DR/BDR无需选择，因为不存在多路访问网段的问题，建立邻居则是自动的。

OSPF 链路类型（Link Type）：首先这个类型和OSPF 邻居相关，和数据链路层介质也有关联，但和网络类型没有对应的关系

1 Stub Network Link ----- 该网段只有一个OSPF ROUTER， 没有邻居，一般LOOPBACK 地址的OSPF接口被认为是Stub Network Link,32 mask.

2 Point to Point Link ---- 接口网络类型为P2P,P2MP的接口类型被认为是是P2P

3 Transit Link  ----- 存在OSPF邻居的接口类型

4  Virtual Link ---- 虚链路，实际上就是手工配置地址的P2P LINK



