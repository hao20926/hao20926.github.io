# linux防火墙设定
防火墙的软件至少提供了三种，而且你不能同时启动任何两种以上的防火墙机制

## 初始化iptables服务
````bash
# 1.查询软件名关键字
yum search iptable*serv*
# 2.查询该软件的说明
yum info iptables-nft-services.noarch
# 3.安装
 yum install iptables-nft-services

rpm -ql iptables-nft-services | egrep 'iptables($|[.-])'
# /etc/sysconfig/iptables                               主要的规则记录
# /etc/sysconfig/iptables-config                        载入模组的记录
# /usr/lib/systemd/system/iptables.service              服务启动的systemd设定档
# /usr/libexec/initscripts/legacy-actions/iptables
# /usr/libexec/iptables
# /usr/libexec/iptables/iptables.init
# 4.先关闭firewalld,nftables之后,启动iptables
systemctl stop nftables
systemctl disable nftables
systemctl stop firewalld
systemctl disable firewalld
systemctl start iptables
systemctl enable iptables

iptables-save
````

### 建立本机防火墙规则执行脚本

````bash
#!/bin/bash
# part 1： 清楚规则并设定预设政策
iptables -F
iptables -X
iptables -Z
iptables -P INPUT   DROP
iptables -P OUTPUT  ACCEPT
iptables -P FORWARD ACCEPT

# part 2； 基础的三条防火墙规则
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT

# part 3； 拒绝黑名单、开放白名单的设定
iptables -A INPUT -s 10.30.30.0/24 -j REJECT
iptables -A INPUT -i enp1s0 -s 192.168.201.254 -j ACCEPT
iptables -A INPUT -i enp2s0 -s 192.168.10.0/24 -m state --state NEW -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -i enp2s0 -s 192.168.20.0/24 -m state --state NEW -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -i enp3s0 -s 192.168.30.0/24 -m state --state NEW -p tcp --dport 22 -j ACCEPT

# part 4： 一般通用放行的网络服务
iptables -A INPUT -m state --state NEW -p tcp --dport  80 -j ACCEPT
iptables -A INPUT -m state --state NEW -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -m state --state NEW -p udp --dport  53 -j ACCEPT
iptables -A INPUT -m state --state NEW -p tcp --dport  53 -j ACCEPT
iptables -A INPUT -m state --state NEW -p tcp --dport  22 -j ACCEPT

# part 5： 不要忘记储存规则
iptables-save > /etc/sysconfig/iptables
````

## 初始化 firewalld 服务

````bash
# 1. 启用 firewalld 服务
systemctl disable --now nftables
systemctl disable --now iptables
systemctl enable --now firewalld
reboot

# 2. 检查目前的 firewalld 服务列表
firewall-cmd --list-all

````

### 领域 （zone） 与服务 （service）
````bash
firewall-cmd --get-zones   # 查看目前有多少的领域在 firewalld

````
### firewalld 的领域 （zone） 意义
````bash

block （默认拒绝）：
所有主动发起到此领域所在网卡的连线默认情况下，都会被拒绝 （reject）！ 不过本机倒是可以主动向外发起连线的。
dmz （非军事区）：
跟前一个小节的说明一样，连接到此领域所在网卡的主机，大部分是提供给互联网访问的正常网络服务， 因为担心此区的主机被攻破而损害其他子网络，因此 dmz 对内部网络 （lan） 的连线会被限制的比较多。
drop （默认丢弃）：
所有主动发起到此领域所在网卡的连线默认情况下，都会被丢弃 （drop） 且不会有任何告知讯息！ 不过本机倒是可以主动向外发起连线的。
external （外部领域）：
默认使用了 SNAT 的伪装 （masquerade） 机制，主要跟外部网络环境连接。 由于跟互联网是可以直接沟通的， 所以也需要特别防护才行！ 仅有放行的封包才可以进入本地网络。
home （家用领域）：
相当于放在家里的桌机，在家里网络环境下，大部分的主机都是可信任的比较多！ 属于可信任度比较高的领域。
internal （内部领域）：
跟 home 有一些些类似，主要放置你比较信任的内网设备。 通常也跟 external 领域做搭配！
public（公开领域）：
是 firewalld 默认的领域，为不信任领域政策，单机用防火墙设定比较常使用这种领域， 你无法信任所有的连接。 但是，可能因为服务器的需要，因此需要放行某些服务之意。
trusted （可信任领域）：
任何连接到此领域的连接都放行！
work （工作领域）：
用来上班工需要作用的电脑，等于是办公室里面的桌机那样的概念，所以旁边都是同事，因此大部分的连线是可信任的， 只是可能还额外放行某些服务就是了。
````

### firewalld 内置服务 （service） 的信息
````bash
firewall-cmd --get-services

firewall-cmd --info-service=ssh

    # ssh
    #     ports: 22/tcp      预设方行的端口号/协议
    #      protocols:         针对的协议
    #      source-ports:    来源端口
    #      modules:          使用的模组
    #      destination:      目的地位置
    #      includes:          其他包含项目
    #      helpers:           其他协助模组

````
### 使用 firewall-cmd 指令实际管理防火墙规则





## 初始化 nftables 服务

````bash

# 1. 三种防火墙软件，开放 nftables 服务：
systemctl disable --now iptables
systemctl disable --now firewalld
systemctl enable --now nftables


# 2. 启用预设的防火墙机制：先修改配置文件 /etc/sysconfig/nftables.conf
vim /etc/sysconfig/nftables.conf
# include "/etc/nftables/main.nft"
# 大概在第 4 行，将注释 # 拿掉即可！

systemctl restart nftables
nft list chains
````

