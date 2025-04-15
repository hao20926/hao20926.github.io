# linux的账号与群组

## /etc/passwd 文件结构
````bash
[root@study ~]# head -n 4 /etc/passwd
root:x:0:0:root:/root:/bin/bash <==等一下做为底下说明用
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
````
每一列使用『:』分隔开，共有七列，分别是：<br/>

1.账号名称<br/>
2.密码（这里显示X,密码数据存放在etc/shadow）<br/>
3.UID（当UID为0时，代表这个账号是『系统管理员』<br/>
            1-999，为系统账号 <br/>
             1000-60000，为可登录账号，一般给使用者用的）<br/>
4.GID (存放在/etc/group)<br/>
5.用户信息说明栏<br/>
6.家目录<br/>
7.Shell<br/>

## /etc/shadow 文件结构
````bash
[root@study ~]# head -n 4 /etc/shadow
root:$6$wtbCCce/PxMeE5wm$KE2IfSJr.YLP7Rcai6oa/T7KFhO...:16559:0:99999:7::: <==底下说明用
bin:*:16372:0:99999:7:::
````
每一列使用『:』分隔开，共有九列，分别是：<br/>

1.账号名称<br/>
2.密码（已加密）<br/>
3.最近更动密码的日期<br/>
4.密码不可被更动的天数<br/>
5.密码需要重新变更的天数<br/>
6.密码需要变更期限前的警告天数<br/>
7.密码过期后的账号宽限时间(密码失效日)<br/>
8.账号失效日期<br/>
9.保留<br/>

## /etc/group 文件结构
````bash
[root@study ~]# head -n 4 /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
````
每一列使用『:』分隔开，共有四列，分别是：<br/>

1.组名
2.群组密码
3.GID
4.此群组支持的账号名称（要使账号加入群组，在后面加入，注意不要有空格，多个用逗号分开）

# 账号管理

## `useradd`
````bash
useradd [-u UID] [-g 初始群组] [-G 次要群组] 

选项与参数：
-u ：后面接的是 UID ，是一组数字。直接指定一个特定的 UID 给这个账号；
-g ：后面接的那个组名就是我们上面提到的 initial group 啦～
 该群组的 GID 会被放置到 /etc/passwd 的第四个字段内。
-G ：后面接的组名则是这个账号还可以加入的群组。
 这个选项与参数会修改 /etc/group 内的相关资料喔！
-M ：强制！不要建立用户家目录！(系统账号默认值)
-m ：强制！要建立用户家目录！(一般账号默认值)
-c ：这个就是 /etc/passwd 的第五栏的说明内容
-d ：指定某个目录成为家目录，而不要使用默认值。务必使用绝对路径！
-r ：建立一个系统的账号，这个账号的 UID 会有限制 (参考 /etc/login.defs)
-s ：后面接一个 shell ，若没有指定则预设是 /bin/bash 的啦～
-e ：后面接一个日期，格式为『YYYY-MM-DD』此项目可写入 shadow 第八字段，
 亦即账号失效日的设定项目啰；
-f ：后面接 shadow 的第七字段项目，指定密码是否会失效。0 为立刻失效，
 -1 为永远不失效(密码只会过期而强制于登入时重新设定而已。
````

## `passwd`
````bash
passwd   [--stdin]   [账号名称]   <==所有人均可使用来改自己的密码

选项与参数：
--stdin ：可以透过来自前一个管线的数据，作为密码输入，对 shell script 有帮助！
-l ：是 Lock 的意思，会将 /etc/shadow 第二栏最前面加上 ! 使密码失效；
-u ：与 -l 相对，是 Unlock 的意思！
-S ：列出密码相关参数，亦即 shadow 文件内的大部分信息。
-n ：后面接天数，shadow 的第 4 字段，多久不可修改密码天数
-x ：后面接天数，shadow 的第 5 字段，多久内必须要更动密码
-w ：后面接天数，shadow 的第 6 字段，密码过期前的警告天数
-i ：后面接『日期』，shadow 的第 7 字段，密码失效日期
````

## `chage` 
````bash
chage [-ldEImMW] 账号名

选项与参数：
-l ：列出该账号的详细密码参数；
-d ：后面接日期，修改 shadow 第三字段(最近一次更改密码的日期)，格式 YYYY-MM-DD
-E ：后面接日期，修改 shadow 第八字段(账号失效日)，格式 YYYY-MM-DD
-I ：后面接天数，修改 shadow 第七字段(密码失效日期)
-m ：后面接天数，修改 shadow 第四字段(密码最短保留天数)
-M ：后面接天数，修改 shadow 第五字段(密码多久需要进行变更)
-W ：后面接天数，修改 shadow 第六字段(密码过期前警告日期)
````

## `usermod`  也可以直接到/etc/passwd或/etc/shadow去修改
````bash
usermod [-cdegGlsuLU] username

选项与参数：
-c ：后面接账号的说明，即 /etc/passwd 第五栏的说明栏，可以加入一些账号的说明。
-d ：后面接账号的家目录，即修改 /etc/passwd 的第六栏；
-e ：后面接日期，格式是 YYYY-MM-DD 也就是在 /etc/shadow 内的第八个字段数据啦！
-f ：后面接天数，为 shadow 的第七字段。
-g ：后面接初始群组，修改 /etc/passwd 的第四个字段，亦即是 GID 的字段！
-G ：后面接次要群组，修改这个使用者能够支持的群组，修改的是 /etc/group 啰～
-a ：与 -G 合用，可『增加次要群组的支持』而非『设定』喔！
-l ：后面接账号名称。亦即是修改账号名称， /etc/passwd 的第一栏！
-s ：后面接 Shell 的实际文件，例如 /bin/bash 或 /bin/csh 等等。
-u ：后面接 UID 数字啦！即 /etc/passwd 第三栏的资料；
-L ：暂时将用户的密码冻结，让他无法登入。其实仅改 /etc/shadow 的密码栏。
-U ：将 /etc/shadow 密码栏的 ! 拿掉，解冻啦！
````

## `userdel`
````bash
userdel [-r] username

选项与参数：
-r ：连同用户的家目录也一起删除
````

## `id`  查询某人或自己的相关 UID/GID 等等的信息
````bash
 id [username]

````

## `finger` (目前系统默认不装)  查阅很多用户相关的信息
````bash
finger [-s] username

选项与参数：
-s ：仅列出用户的账号、全名、终端机代号与登入时间等等；
-m ：列出与后面接的账号相同者，而不是利用部分比对 (包括全名部分)
````

## `chfn`
````bash
chfn [-foph] [账号名]

选项与参数：
-f ：后面接完整的大名；
-o ：您办公室的房间号码；
-p ：办公室的电话号码；
-h ：家里的电话号码！
````

## `chsh`  change shell
````bash
 chsh [-ls] 

选项与参数：
-l ：列出目前系统上面可用的 shell ，其实就是 /etc/shells 的内容！
-s ：设定修改自己的 Shell 啰
````

## `groupadd`
````bash
 groupadd [-g gid] [-r] 组名

选项与参数：
-g ：后面接某个特定的 GID ，用来直接给予某个 GID ～
-r ：建立系统群组啦！与 /etc/login.defs 内的 GID_MIN 有关。
````

## `groupmod`
````bash
groupmod [-g gid] [-n group_name] 群组名

选项与参数：
-g ：修改既有的 GID 数字；
-n ：修改既有的组名
````

## `groupdel`
````bash
groupdel [groupname]

````

## `gpasswd`  群组管理员功能
````bash
(Root)
gpasswd groupname 
gpasswd [-A user1,...] [-M user3,...] groupname   
gpasswd [-rR] groupname

选项与参数：
 ：若没有任何参数时，表示给予 groupname 一个密码(/etc/gshadow)
-A ：将 groupname 的主控权交由后面的使用者管理(该群组的管理员)
-M ：将某些账号加入这个群组当中！
-r ：将 groupname 的密码移除
-R ：让 groupname 的密码栏失效

(Group administrator)
gpasswd [-ad] user groupname

选项与参数：
-a ：将某位使用者加入到 groupname 这个群组当中！
-d ：将某位使用者移除出 groupname 这个群组当中。
````
