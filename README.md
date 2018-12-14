# DVWA-SQL-注入实验
赵梓钧-15300240008
## 实验内容
激活下载的虚拟机镜像，以普通用户身份hacker登录进入虚拟机操作系统（不需要口令），可查看网络设置（192.168.40.128）

![](https://i.imgur.com/GFhDdvz.png)



调整虚拟网络配置，使虚拟机可被远程连接。
![](https://i.imgur.com/7294oE4.png)


若因特殊需要，必须改变配置(或关机)，可利用hacker目录下的堆溢出漏洞程序heap获得root权限。但不允许利用root权限直接获取DVWA数据库信息。
连接DVWA 的页面(http://192.168.40.128/dvwa)，用户名admin，口令password(缺省设置)。

![](https://i.imgur.com/OqK4gdU.png)


设置DVWA安全级别（左侧DVWA Security按钮，网上可查询相关说明）

![](https://i.imgur.com/jvRqJkD.png)



进入DVWA SQL攻击界面（左侧SQL Injection按钮），进行SQL注入攻击（网上查询相关攻略）
![](https://i.imgur.com/F7lACQx.png)
![](https://i.imgur.com/jENwv1D.png)


这应该是从用户列表里查询first name 和surname这两列的内容，select的依据是user id那一列的内容等于’我们的输入’
使用单引号让’我们的输入’提前闭合，形成 ‘1’其他的信息’新目标’
采取各种方法（手工注入，自动攻击工具等），试图获得dvwa数据库中的所有表的内容。

![](https://i.imgur.com/J7iohQr.png)


首先获取数据库的名称，数据库名就叫dvwa

![](https://i.imgur.com/DdjYciX.png)



然后看看数据库里的表的名称，有三张表，依次找出每个字段的名称
![](https://i.imgur.com/UG4Lydc.png)


![](https://i.imgur.com/lPcwqhn.png)


![](https://i.imgur.com/qKN4TPB.png)


这样就可以根据字段进行查询了，将查询结果划分为两大部分正好显示在输出的fierstname栏目和surname栏目
![](https://i.imgur.com/j1Mnj65.png)


![](https://i.imgur.com/COH0Lfn.png)


![](https://i.imgur.com/gBMLhWt.png)






