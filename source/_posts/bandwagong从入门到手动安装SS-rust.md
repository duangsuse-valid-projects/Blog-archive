---
title: bandwagong从入门到手动安装SS-rust
thumbnail: 'https://duangsuse.gitlab.io/blog/img/thumb/bwg_ssrust.png'
date: 2017-07-30 23:38:31
categories: 系统管理
tags: rust 后端 梯子
---
# 事情的起因
某年某月某天,duangsuse突然停止了咸鱼~~其实仅仅只是完成了一件拖了很久的事情罢了~~,下定决心将之前盼望已久的电脑买下.
具体花销见下表,实际花的钱更少一些.
```plain
***start
#total: 5376
= PC 4424
x 主机 https://item.taobao.com/item.htm?id=528347723998 3000
x 显示器 https://detail.tmall.com/item.htm?id=545087400148 699
x mouse https://detail.tmall.com/item.htm?id=19229169725 22
x keyboard https://detail.tmall.com/item.htm?id=13570491314 399
x 额外硬盘 https://detail.tmall.com/item.htm?id=532585923545 289
x HDMI接线 https://item.taobao.com/item.htm?id=42946068535 15
= Orange Pi 282
x PC2 https://item.taobao.com/item.htm?id=541111439522 128
x zero https://item.taobao.com/item.htm?id=541063652212 62
x TF https://item.taobao.com/item.htm?id=536688543918 52
x 电源线 https://item.taobao.com/item.htm?id=524038384425 8
x 摄像头 https://item.taobao.com/item.htm?id=523160733840 32
= VPS 570
x rkecloud https://bitbear.net/vpshost 360
x bandwagon https://bandwagonhost.com/vps-hosting.php ~~210~~ 188
= YangChongHou 100
x monkey https://coding.net/shop 100
***end
```
最后回家的路上(拆迁,于是就分新家旧家了..),突然发现有件东西买的不合我的想法...
就是那个万恶的`搬瓦工`..我定了四月的,然而我想等一两年后再用(作为Dropage的软件仓库,其实要求也不高,毕竟只存储DROPBUILD脚本.)
于是我只好放弃了>_> 不动锐壳~~(现已被有关部门强制关停,改名换姓比特熊逃过死劫...)~~上的钱
手头急需SS来爬墙,于是我的3个小时就这样贡献出去了..

# 购买服务
bandwagong的服务注册地址就不用说了吧?随便填完表(强迫症说明:Zip code填邮编即可),选好计划然后付钱...
# 重新部署系统
>如果你也想和duangsuse一样发现Kiwi的一键SS脚本会失败以后尝试在CentOS6上编译安装shadowsocks-rust的话,你会die的.
>想想编译GCC的恐惧吧...2.1G的Git库,即使只是7.x源代码包都很大...
>我自然看过烧饼写得文章,说博客上最好不要教人编译,应该用pm,但是CentOS这稳定性的天下根本pm不到4.4以上的版本,而碰巧ss-rust的ring依赖库编译时需要~~原谅~~它...
>当然,如果你只是寻求ss服务的话可以尝试在包管理器中寻找(CentOS里没有噢! 或者自行编译...可以的...
>为什么GCC4.4不行?因为不支持-std=c1x参数...

duangsuse技术渣建议__Debian9 x86_64__ ,因为对自由软件更友好~~更傻瓜化~~
当然`rust`和`libsodium`编译环境必须有.
使用这些命令安装`rust`和定制编译环境,本吊建议上nightly工具链..
```shell
apt update&&apt install curl gcc make libssl-dev pkg-config #我不会告诉你不安libssl-dev编译不过,你试试?
curl https://sh.rustup.rs -sSf | sh
```

# 编译libsodium
当然,如你所见,是一个库,而且是一个加密库.
编译不需要多少时间所以可以放心.
```shell
wget https://github.com/jedisct1/libsodium/releases/download/1.0.13/libsodium-1.0.13.tar.gz
gunzip -d libsodium-1.0.13.tar.gz
tar -xf libsodium-1.0.13.tar
cd libsodium-1.0.13
./configure
make&&make test
make install
```
# 编译shadowsocks-rust
好耶~最后一步!
一条命令就可以解决:
__cargo install shadowsocks-rust__

# 最后
~~祝您身体健康~~ ~~别过度了#(滑稽)~~
随便写个服务器配置,然后用supervisor或systemd什么的让它持续运行下去就行了
~~这个我就不说了吧,,,~~
建议:Server端
```json
{
    "server": "my_server_ip",
    "server_port": 8388,
    "password": "mypassword",
    "timeout": 300,
    "method": "aes-256-cfb"
}
```
Client端
```json
{
    "server": "my_server_ip",
    "server_port": 8388,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "mypassword",
    "timeout": 300,
    "method": "aes-256-cfb"
}
```
systemd配置文件)/lib/systemd/system/ss.service:
```toml
[Unit]
Description=Shadowsocks-rust server
After=network.target

[Service]
ExecStart=/root/.cargo/bin/ssserver --config /root/config.json

```
