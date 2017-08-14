---
title: 一根网线ssh刚安装完的开发板
thumbnail: 'https://duangsuse.gitlab.io/blog/img/thumb/opi.png'
date: 2017-08-15 01:59:02
categories:
tags: DHCP Web ip 开发板 
---
今天将PC2重新安了Ubuntu Server.于是在没有HDMI输出对象和键盘的情况下愿把PC降格为路由器连上板子.
# 说几句无关的
\>_> 内个我最近有要发的博文没写,近几天补完,大家别急( ~~虽然我知道没几个人~~
国内的话推荐买__Orange PI__而不是__Raspberry Pi__. `因为便宜`.... 
duangsuse以前听说Raspberry Pi Zero刚出被抢价格疯涨还有人买表示奇怪,一个板子吗~ ~~又不是不能用~~
# 开始正文
你需要运行一个`DHCP Server`来让你的板子正常连接你的PC. PC的操作系统建议是`GNU/Linux`.我是在`Arch Linux`上进行演示的.
第一步,确保你的板子已经正常开机,如果没有的话反正就是用网线把电脑和板子接好后~~会亮~~就行了 ~~当然注意指示灯也行~~
# 环境准备
你需要安装 __kea__ 这是一个不错的DHCP Server.现在已经进了Arch的community源了.
还需要 __netdiscover__ 这是一款局域网扫描的~ 我们用它的目的就是拿到板子分配到的ip地址.当然,这个在主源里不提供,blackarch的 ~~我也不会告诉你可以拿arp指令查看路由表~~
既然是linux党的话我想就不需要人教了.你们自己安吧(
# set up dhcp server
区别于老式的ifconfig系列工具,我们将使用ip工具.
1. `ip address` 从中找出你的以太网设备,然后>
2. `ip link set up dev {}` (你的设备)
3. `ip addr add 139.96.30.100/24 dev {}` (同上)
~~我不会告诉你们以上命令全是Arch wiki抄的,dalao社区万岁!~~
接下来我们会使用ipv4 的 dhcp服务器.
编辑`/etc/kea/kea.conf`,
```json
"Dhcp4": {
    // Add names of your network interfaces to listen on.
    "interfaces-config": {
        // See section 8.2.4 for more details. You probably want to add just
        // interface name (e.g. "eth0" or specific IPv4 address on that
        // interface name (e.g. "eth0/192.0.2.1").
        "interfaces": ["enp3s0"] //replace with your own device
```
不用我说你们肯定民白吧(
然后 `kea-dhcp4 -c /etc/kea/kea.conf`
ok,现在你的板子应该拿到分配的ip地址了.
# ssh板子
netdiscover来查板子的ip.
`ssh orangepi@{ip}` 来连接.
Orage Pi的默认密码和用户名相同. Ubuntu Server不应以root登录,相反,您应该总是使用sudo -i,这是为了安全,也透露了GNU/Linux往往比Windows安全的一大原因:
Windows默认Admin,GNU/Linux社区讨厌日常使用root登录的人.
然后呢( 比如怎么联网什么的
duangsuse也不知道
不如在主机上开个proxy server,板子连上如何? :-( 
never too Naive to learn
