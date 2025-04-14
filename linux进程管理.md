# linux进程管理

## `jobs` 观察目前的背景工作状态
````bash
 jobs [-lrs]

选项与参数：
-l ：除了列出 job number 与指令串之外，同时列出 PID 的号码；
-r ：仅列出正在背景 run 的工作；
-s ：仅列出正在背景当中暂停 (stop) 的工作。
````

## `bg`   让工作在背景下的状态变成运作中(后台)
````bash
ex.
jobs ;  bg %1 ;  jobs
[1]+  已停止               vim /etc/crontab
[1]+ vim /etc/crontab &
[1]+  运行中               vim /etc/crontab &
"&" 该符号就代表该工作被启动在背景当中
````
## `kill`  管理背景当中的工作
````bash
 kill -signal %jobnumber

选项与参数：
-l ：这个是 L 的小写，列出目前 kill 能够使用的讯号 (signal) 有哪些？
signal ：代表给予后面接的那个工作什么样的指示啰！用 man 7 signal 可知：
 -1 ：重新读取一次参数的配置文件 (类似 reload)；
 -2 ：代表与由键盘输入 [ctrl]-c 同样的动作；
 -9 ：立刻强制删除一个工作；
 -15：以正常的进程方式终止一项工作。与 -9 是不一样的。
````
## `pgrep`  根据进程名称或其他属性快速查找进程 ID（PID）
````bash
安装pgrep（大多数Linux发行版预装）
Ubuntu/Debian
sudo apt-get install procps

CentOS/RHEL
sudo yum install procps-ng

Arch Linux
sudo pacman -S procps-ng

用法：
pgrep -[lionuvc] 进程名

选项与参数：
    默认只显示PID

  -l ： 同时显示进程名和PID
 -o ： 当匹配多个进程时，显示进程号最小的那个
 -n ： 当匹配多个进程时，显示进程号最大的那个
注：进程号越大，并不一定意味着进程的启动时间越晚
 -v ： 反向匹配（排除特定进程）
 -n :   找到特定进程中最新的进程
 -c :   统计符合条件的进程数量
````

## `ps`  将某个时间点的进程运作情况摘取下来
````bash
 ps aux   <==观察系统所有的进程数据
 ps -lA    <==也是能够观察所有系统的数
 ps axjf   <==连同部分进程树状态

选项与参数：
-A ：所有的 process 均显示出来，与 -e 具有同样的效用；
-a ：不与 terminal 有关的所有 process ；
-u ：有效使用者 (effective user) 相关的 process ；
x ：通常与 a 这个参数一起使用，可列出较完整信息。
输出格式规划：
l ：较长、较详细的将该 PID 的的信息列出；
j ：工作的格式 (jobs format)
-f ：做一个更为完整的输出。
````
ex.
````bash
[root@hao2 ~]# ps -l
F S   UID     PID    PPID    C  PRI  NI     ADDR   SZ  WCHAN           TTY          TIME        CMD
4 S     0  582037  582036    0  80   0       -    2266 do_wai        pts/0     00:00:00    bash
0 T     0  582667  582037    0  80   0       -    1858 do_sig        pts/0     00:00:00     man
0 T     0  582683  582667    0  80   0       -    1542 do_sig        pts/0     00:00:00     less
4 R     0  582830  582037    0  80   0       -    2575 -             pts/0     00:00:00      ps
````
F：代表这个进程旗标 (process flags)，说明这个进程的总结权限，常见号码有：<br/>
      若为 4 表示此进程的权限为 root ；<br/>
      若为 1 则表示此子进程仅进行复制(fork)而没有实际执行(exec)。<br/>


S：代表这个进程的状态 (STAT)，主要的状态有：<br/>
      R (Running)：该程序正在运作中；<br/>
      S (Sleep)：该程序目前正在睡眠状态(idle)，但可以被唤醒(signal)。<br/>
      D ：不可被唤醒的睡眠状态，通常这支程序可能在等待 I/O 的情况(ex>打印)<br/>
      T ：停止状态(stop)，可能是在工作控制(背景暂停)或除错 (traced) 状态；<br/>
      Z (Zombie)：僵尸状态，进程已经终止但却无法被移除至内存外。<br/>


UID/PID/PPID：代表『此进程被该 UID 所拥有/进程的 PID 号码/此进程的父进程 PID 号码』<br/><br/><br/>


C：代表 CPU 使用率，单位为百分比；<br/><br/><br/>
 
PRI/NI：Priority/Nice 的缩写，代表此进程被 CPU 所执行的优先级，数值越小代表该进程越快被 CPU 执行。<br/><br/><br/>


ADDR/SZ/WCHAN：都与内存有关，ADDR 是 kernel function，指出该进程在内存的哪个部分，如果是个<br/>
running 的进程，一般就会显示『 - 』 / SZ 代表此进程用掉多少内存 / WCHAN 表示目前进程是否运作中，<br/>
同样的， 若为 - 表示正在运作中。<br/><br/><br/>


TTY：登入者的终端机位置，若为远程登录则使用动态终端接口 (pts/n)；<br/><br/><br/>


TIME：使用掉的 CPU 时间，注意，是此进程实际花费 CPU 运作的时间，而不是系统时间；<br/><br/><br/>


CMD：就是 command 的缩写，造成此进程的触发程序之指令为何。<br/><br/><br/>



##  `top` 动态观察进程的变化
````bash
top [-d 数字] | top [-bnp]

选项与参数：
-d ：后面可以接秒数，就是整个进程画面更新的秒数。预设是 5 秒；
-b ：以批次的方式执行 top ，还有更多的参数可以使用喔！
 通常会搭配数据流重导向来将批次的结果输出成为文件。
-n ：与 -b 搭配，意义是，需要进行几次 top 的输出结果。
-p ：指定某些个 PID 来进行观察监测而已。
在 top 执行过程当中可以使用的按键指令：
? ：显示在 top 当中可以输入的按键指令；
P ：以 CPU 的使用资源排序显示；
M ：以 Memory 的使用资源排序显示；
N ：以 PID 来排序喔！
T ：由该 Process 使用的 CPU 时间累积 (TIME+) 排序。
k ：给予某个 PID 一个讯号 (signal)
r ：给予某个 PID 重新制订一个 nice 值。
q ：离开 top 软件的按键
````

## `pstree`
````bash
pstree [-A|U] [-up]

选项与参数：
-A ：各进程树之间的连接以 ASCII 字符来连接；
-U ：各进程树之间的连接以万国码的字符来连接。在某些终端接口下可能会有错误；
-p ：并同时列出每个 process 的 PID；
-u ：并同时列出每个 process 的所属账号名称。
````


## `nice`  新执行的指令即给予新的 nice 值(优先级)
````bash
nice [-n 数字] command

选项与参数：
-n ：后面接一个数值，数值的范围 -20 ~ 19

````

## `renice` 已存在进程的 nice 重新调整
````bash
renice [number] PID

选项与参数：
PID ：某个进程的 ID 
````

## `free`  观察内存使用情况
````bash
 free [-b|-k|-m|-g|-h] [-t] [-s N -c N]

选项与参数：
-b ：直接输入 free 时，显示的单位是 Kbytes，我们可以使用 b(bytes), m(Mbytes)
 k(Kbytes), 及 g(Gbytes) 来显示单位喔！也可以直接让系统自己指定单位 (-h)
-t ：在输出的最终结果，显示物理内存与 swap 的总量。
-s ：可以让系统每几秒钟输出一次，不间断的一直输出的意思！对于系统观察挺有效！
-c ：与 -s 同时处理～让 free 列出几次的意思
````

## `uname`  查阅系统与核心相关信息
````bash
uname [-asrmpi]

选项与参数：
-a ：所有系统相关的信息，包括底下的数据都会被列出来；
-s ：系统核心名称
-r ：核心的版本
-m ：本系统的硬件名称，例如 i686 或 x86_64 等；
-p ：CPU 的类型，与 -m 类似，只是显示的是 CPU 的类型！
-i ：硬件的平台 (ix86)
````


## `uptime`  观察系统启动时间与工作负载
````bash
uptime
````

## `netstat` 追踪网络或插槽文件
````bash
netstat -[atunlp]

选项与参数：
-a ：将目前系统上所有的联机、监听、Socket 数据都列出来
-t ：列出 tcp 网络封包的数据
-u ：列出 udp 网络封包的数据
-n ：不以进程的服务名称，以埠号 (port number) 来显示；
-l ：列出目前正在网络监听 (listen) 的服务；
-p ：列出该网络服务的进程 PID
````

## `dmesg`  分析核心产生的讯息(系统开机时的信息)
````bash
[root@study ~]# dmesg | more

搜寻开机的时候，硬盘的相关信息为何？
[root@study ~]# dmesg | grep -i vda

````

## `vmstat` 侦测系统资源变化
````bash
[root@study ~]# vmstat [-a] [延迟 [总计侦测次数]] 

选项与参数：
-a ：使用 inactive/active(活跃与否) 取代 buffer/cache 的内存输出信息；
-f ：开机到目前为止，系统复制 (fork) 的进程数；
-s ：将一些事件 (开机至目前为止) 导致的内存变化情况列表说明；
-S ：后面可以接单位，让显示的数据有单位。例如 K/M 取代 bytes 的容量；
-d ：列出磁盘的读写总量统计表
-p ：后面列出分区槽，可显示该分区槽的读写总量统计表

统计目前主机 CPU 状态，每秒一次，共计三次！

 vmstat 1 3
````
