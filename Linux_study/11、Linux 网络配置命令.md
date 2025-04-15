# linux 网络配置

##  `ifconfig`

ifconfig 命令用来配置网络或显示当前网络接口状态

### 设置网络接口参数-ifconfig
````bash
ifconfig DEVICE IP netmask NETMASK    ##设置ip地址

ifconfig eth0 192.168.168.1/24
````

###  禁用(临时)或者重新激活网卡
````bash
ifconfig 网络接口 up
ifconfig 网络接口 down
````
### 设置虚拟网络接口
相当于在一个网卡上配置多个IP地址
````bash
格式（示例)： ifconfig 网络接口:序号 IP地址
ifconfig eth33:1 11.11.11.11
````

## `ip`
````bash
ip addr show (网卡名称)  #显示网卡的IP地址和相关信息
ip link show # 显示所有网络接口的状态信息
````
###配置网络接口
````bash
ip addr add [ip/mask] dev [interface] ：为指定网络接口添加IP地址
ip addr change [ip/mask] dev [interface] ：为指定网络接口修改IP地址
ip addr del [ip/mask] dev [interface] ：从指定网络接口删除IP地址
ip link set dev [interface] up/down ：启用或禁用指定的网络接口
````
## Route命令

### 观察路由表信息
````bash
route [-nee]
-n ：不要使用通讯协定或主机名称，直接使用 IP 或 port number；
-ee ：使用更详细的资讯来显示
````

### 添加路由
````bash
route add [-net|-host] [网域或主机] netmask [mask] [gw|dev]
route del [-net|-host] [网域或主机] netmask [mask] [gw|dev]
参数：
-net ：表示后面接的路由为一个网域（网段）的路由；
-host ：表示后面接的为连接到单部主机的路由；
netmask ：掩码，决定了网域的大小（配合-net使用，构成一个网段）；
gw ：gateway 的简写，后续接的是 IP （必须和本机的其中一块网卡处于同一网段），与 dev 不同；
dev ：如果只是要指定由哪一块网卡连线出去，则使用这个设定，后面接 eth0了，eth1 等
````
### 删除路由
````bash
格式：
route del -net {NETWORK-ADDRESS} netmask {NETMASK} reject
````

## netstat命令


netstat命令用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，
一般用于检验本机各端口的网络连接情况。netstat是在内核中访问网络及相关信息的程序，
它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。


##  Linux 全新网络管理工具 nmcli 的使用

### 查看 NM 纳管状态
`nmcli n`
### 开启 NM 接管
`nmcli n on`
### 关闭 NM 纳管（谨慎执行）
`nmcli n off`

## nmcli connection 配置
### 删除 connection （类似于 ifdown 并删除 ifcfg ）
`nmcli c delete enp0s3`
### 查看 connection 列表
`nmcli c show`
### 查看 connection 详细信息
`nmcli c show enp0s3`
### 重载配置文件（不会马上生效）
`nmcli c reload`
### 立即生效 connection ，有 3 种方法：
`nmcli c up ens32`

`nmcli d reapply enp0s3`  专门用于刷新 connection ，前提是网卡的 device 处于 connected 状态，否则会报
错

`nmcli d connect enp0s3`  刷新该网卡对应的活跃 connection


## nmcli device 配置
### 查看 device 列表
`nmcli d`
### 查看所有 device 详细信息
`nmcli d show`
### 查看指定 device 的详细信息
`nmcli d show enp0s3`
### 激活网卡
`nmcli d connect enp0s3`


