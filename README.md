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

OSPF 外部路由 ---- OSPF域外的其它路由协议重分布到域内； 或者其他OSPF进程的路由重分布到域内

外部路由分OE1;OE2; 区别在于OE2的外部路由METRIC对所有域内路由器都是一样的，比如为20，而OE1，除了外部路由进入之前的METRIC之外，还包括其他路由器到ASBR的COST值，总COST必须是二者之和

OSPF 末节区域 ---- 1 LSA 网络泛洪 增加网络负荷，降低ROUTER处理能力 2 本身很多ROUTER处理能力就比较弱，无法处理众多LSA ，设置末节区域有效降低网络负荷，降低对全网网络设备的处理要求，节约成本。

1  Stub Area  ---- ABR 不会将外部路由发布进来，同时该区域也不会重分布外部路由进来（该区域没有ASBR），但是其它区域的汇总路由可以从ABR发进来，同时会有一条默认路由指向ABR自己发到区域内部

2  Totally Stub Area --- 和 Stub Area 其它保持一致，另外就是其它区域的汇总路由也不可以发进来，全靠默路由

3  Not So Stub Area(NSSA)--- ABR不会将外部路由发布进来，但是其它区域的汇总路由可以从ABR发进来,而且可以在该区域内重分布外部路由（可以由ASBR），那么默认的情况就是ABR不会发默认路由，但是也可以手工发送，毕竟其它区域的外部路由可以通过默认路由导入

4  Totally Not So Stub Area ----其余和NSSA一致，不同在于其它区域的汇总路由也不可以从ABR发进来，所以需要自动发送一条默认路由。

OSPF LSA Type: 

LSA1 --- Route Link , OSPF 路由器产生，包含该路由器所有OSPF接口的打包信息，只能在区域内发送

LSA2 --- Network Link, OSPF DR 产生，相当于每个网段产生一条LSA2

LSA3 --- Summay Link , ABR 产生，将区域内LSA1进行汇总简化发往外区域

LSA4 --- ASBR Summary Link ,ABR产生，发往外部区域，告知本区域内ASBR的Router-ID,配合LSA5使用，LSA5的Router-ID为ASBR的，ABR不能修改，外部区域路由器无法获知，必须由ABR发送一条LSA4告知如何到达ASBR

LSA5 --- External Link, 由ASBR发送，包含ASBR 的Router-ID 和Forward Address,外部路由重分布进入OSPF域后，ASBR需要告知如何到达该
外部路由
  
如何确定forward address,分两种情况：

1 ASBR到外部路由的下一跳地址就是forward address，如果该网段在OSPF进程中，则通过LSA5，所有OSPF路由器均可以直接路由到该forward address(10.3.1.5)

2 如果该地址网段不在OSPF进程中，则forward address= 0.0.0.0,所有OSPF路由器通过外部LSA的Router-ID(4.4.4.4)去往外部路由

OSPF 虚链路 ----- 对于无法直连到AREA0的区域，需要将AREA0扩展到与其相邻的状态。扩展的这个区域称为transit area，该区域的两个ABR之间的LINK 成为virtual link. 虚链路也要建立邻居，但是不会交换HELLO 包
