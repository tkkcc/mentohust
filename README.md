# 锐捷多拨
[blog](https://tkkcc.github.io/2017/09/05/mentohust/)
## 原由：

某高校采用锐捷认证，限速明显。

## 思路：

路由器mentohust多进程多号认证。

## 实战：

- 斐讯k2
- 两个校园网账号
### 1. k2刷支持多拨的固件如pandorabox 

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(7).png?raw=true)

### 2. 使用winscp上传mentohust至bin目录

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClidp.png?raw=true)

有些固件重启清目录，不会搞；有些固件bin不能放文件，放其他目录也可以

### 3. 多拨

   路由器web管理界面设置`虚拟wan`

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip.png?raw=true)

   按` 保存&应用`即可，这里要等等

   虚拟接口默认为pppoe拨号

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(5).png?raw=true)

   而锐捷为DHCP，所以要修改

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(4).png?raw=true)

   按下`切换协议`后按` 保存&应用`， 每个要用的接口都要设置

### 4. xshell
设置mentohust权限
   ```shell
   chmod 777 /bin/mentohust
   ```
由于我要后台前台运行调来调去，为便于调试不让mentohust写入配置文件

   ```shell
   echo > /etc/mentohust.conf 
   chmod 000 /etc/mentohust.conf
   ```

直接运行看看有哪些网卡，记录网卡名

   ```shell
    [root@PandoraBox_D141:/root]#mentohust

   欢迎使用MentoHUST	版本: 0.3.1

   Copyright (C) 2009-2010 HustMoon Studio

   人到华中大，有甜亦有辣。明德厚学地，求是创新家。

   Bug report to http://code.google.com/p/mentohust/issues/list

   ** 网卡[1]:	eth0

   ** 网卡[2]:	rai0

   ** 网卡[3]:	ra0

   ** 网卡[4]:	br-lan

   ** 网卡[5]:	eth0.1

   ** 网卡[6]:	macvlan1

   ** 网卡[7]:	eth0.2

   ** 网卡[8]:	macvlan2

   ** 网卡[9]:	macvlan3

   ** 网卡[10]:	macvlan4

   ** 网卡[11]:	macvlan5

   ** 网卡[14]:	nflog

   ** 网卡[15]:	nfqueue

   ?? 请选择网卡[1-15]: 
   ```

按ctrl+c退出，试试单账号能不能跑

用户名：1111111，密码：22222，组播地址：锐捷 DHCP方式：二次认证 网卡：macvlan1

   ```shell
   [root@PandoraBox_D141:/root]#mentohust -u11111111 -p22222  -a1 -d1 -nmacvlan1

   欢迎使用MentoHUST	版本: 0.3.1
   Copyright (C) 2009-2010 HustMoon Studio
   人到华中大，有甜亦有辣。明德厚学地，求是创新家。
   Bug report to http://code.google.com/p/mentohust/issues/list

   ** 用户名:	111111111
   ** 网卡: 	macvlan1
   ** 认证超时:	8秒
   ** 心跳间隔:	30秒
   ** 失败等待:	15秒
   ** 允许失败:	8次
   ** 组播地址:	锐捷
   ** DHCP方式:	二次认证
   ** DHCP脚本:	dhclient/n** 本机MAC:	00:7b:9e:bb:00:21
   ** 使用IP:	172.10.12.170
   ** 子网掩码:	255.255.255.0
   ** 认证参数已成功保存到/etc/mentohust.conf.
   >> 寻找服务器...
   V4 type: 0
   ** 认证MAC:	00:4a:h9:1y:68:ed
   >> 发送用户名...
   V4 type: 0
   >> 发送密码...
   V4 type: 3
   >> 认证成功!
   $$ 系统提示:	?????1t1¤′?°θ
   $$ 计费提示:	?μ±???μ?t??internet;
   ??§???0.00?;
   ?????a30?°
   ?μ±???μ?????internet2;
   >> 正在获取IP...
   dhclient
   /bin/sh: dhclient: not found
   >> 操作结束。
   ** 本机MAC:	00:7b:9e:bb:00:21
   ** 使用IP:	172.17.12.171
   ** 子网掩码:	255.255.255.0
   >> 寻找服务器...
   V4 type: 0
   >> 发送用户名...
   V4 type: 0
   >> 发送密码...
   V4 type: 2
   >> 认证成功!
   $$ 系统提示:	?????1t1¤′?°θ
   $$ 计费提示:	?μ±???μ?t??internet;
   ??§???0.00?;
   ?????a30?°
   ?μ±???μ?????internet2;
   >> 发送心跳包以保持在线...
   ```

能上网则看两个能不能跑，按ctrl+c退出

先上一个，用后台运行

   ```shell
   #先上一个，用后台运行，-b1 ，也可以开两个终端来试

   [root@PandoraBox_D141:/root]#mentohust -u11111111 -p222222  -a1 -d1 -nmacvlan1 -b1

   欢迎使用MentoHUST	版本: 0.3.1

   Copyright (C) 2009-2010 HustMoon Studio

   人到华中大，有甜亦有辣。明德厚学地，求是创新家。

   Bug report to http://code.google.com/p/mentohust/issues/list

   进入后台运行模式，使用参数-k可退出认证。
   ```

能上网则继续，这时的`负载均衡` macvlan1也会绿

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/before.png?raw=true)

**删去pid文件，使得mentohust能多进程运行（关键）**

   ```shell
   [root@PandoraBox_D141:/root]#rm /var/run/mentohust.pid -f
   ```

上第二个 不使用-b1先看看，网卡用不同的 如macvlan2
   ```shell
   [root@PandoraBox_D141:/root]#mentohust -u333333333 -p4444  -a1 -d1 -nmacvlan2

   欢迎使用MentoHUST	版本: 0.3.1
   Copyright (C) 2009-2010 HustMoon Studio
   人到华中大，有甜亦有辣。明德厚学地，求是创新家。
   Bug report to http://code.google.com/p/mentohust/issues/list

   ** 用户名:	3333333333
   ** 网卡: 	macvlan2
   ** 认证超时:	8秒
   ** 心跳间隔:	30秒
   ** 失败等待:	15秒
   ** 允许失败:	8次
   ** 组播地址:	锐捷
   ** DHCP方式:	二次认证
   ** DHCP脚本:	dhclient/n** 本机MAC:	00:7b:9e:bb:00:21
   ** 使用IP:	172.16.12.171
   ** 子网掩码:	255.255.255.0
   ** 认证参数已成功保存到/etc/mentohust.conf.
   >> 寻找服务器...
   V4 type: 0
   ** 认证MAC:	00:4a:h9:1y:68:ed
   >> 发送用户名...
   V4 type: 0
   >> 发送密码...
   V4 type: 3
   >> 认证成功!
   $$ 系统提示:	?????1t1¤′?°θ
   $$ 计费提示:	?μ±???μ?t??internet;
   ??§???0.00?;
   ?????a30?°
   ?μ±???μ?????internet2;
   >> 正在获取IP...
   dhclient
   /bin/sh: dhclient: not found
   >> 操作结束。
   ** 本机MAC:	00:7b:9e:bb:00:21
   ** 使用IP:	172.18.25.171
   ** 子网掩码:	255.255.255.0
   >> 寻找服务器...
   V4 type: 0
   >> 发送用户名...
   V4 type: 0
   >> 发送密码...
   V4 type: 2
   >> 认证成功!
   $$ 系统提示:	?????1t1¤′?°θ
   $$ 计费提示:	?μ±???μ?t??internet;
   ??§???0.00?;
   ?????a30?°
   ?μ±???μ?????internet2;
   >> 发送心跳包以保持在线...
   ```

看到`发送心跳包以保持在线`也不一定ok，先看看负载均衡有没有绿
   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(2).png?raw=true)

   再跑个下载看看两个接口是不是都有

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(6).png?raw=true)

流量走的平均说明成功了。



### 5. 其他
- 前台运行退出用 mentohust -k，但对于pid文件被删掉的进程要用ps查看进程pid再kill掉

   ```shell
   [root@PandoraBox_D141:/root]#ps |grep mentohust
   14093 root      2636 S    mentohust -u111111111 -p22222 -a1 -d1 -nmacvlan2 -b1
   14502 root      2636 S    mentohust -u333333333 -p444444 -a1 -d1 -nmacvlan3 -b1
   16645 root      1508 S    grep mentohust
   [root@PandoraBox_D141:/root]#kill 14093
   ```

- **防火墙设置**不要瞎改，默认就好

- 目录下有k2的mentohust，来自某博主（忘了），支持v4，其他来自恩山无线论坛和qq群

- 新加坡do用aira2拖回本地对比

  单号：

  ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(8).png?raw=true)

  双号：

  ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/ScreenClip(3).png?raw=true)

- mentohust会占用*-test，所以还得舍友牺牲才能搞

- 写在开机脚本里
   ```shell
   mentohust -u1141010106 -p123456  -a1 -d1 -neth0.2 -b1
   rm /var/run/mentohust.pid -f
   sleep 30s
   mentohust -u1151222143 -pau87ddde  -a1 -d1 -nmacvlan1 -b1
   # mentohust -u1160670531 -p120534711  -a1 -d1 -nmacvlan2 -b1
   # mentohust -u1170340333 -p35374e3  -a1 -d1 -nmacvlan1 -b1
   ```

- 恩山多大神

   ![ScreenClip(7).png](https://github.com/tkkcc/mentohust/blob/master/include/213.jpg?raw=true)

