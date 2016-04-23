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

选举DR/BDR的原则： 首先看接口优先级，IP OSPF PRIORITY(0~255)，默认为1 ，数值越大优先级越高，为0 代表不参与选举
