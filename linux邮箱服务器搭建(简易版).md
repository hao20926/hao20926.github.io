# linux邮箱服务器搭建(简易版)
## Rocky 9 邮箱配置示例
在升级到Rocky9及以上版本时，如何安装并配置替代mailx的s-nail以及postfix服务，包括设置SMTP、认证信息，并演示了发送测试邮件的过程。
<br/>
安装postfix s-nail,记得开启服务<br/>

````bash
[root@rocky9 ~]# yum install -y postfix s-nail
````
配置s-nail配置文件

````bash
 如果使用旧版配置文件，如：
set from=xxx@qq.com
set smtp=smtp.qq.com
set smtp-auth-user=xxx@qq.com
set smtp-auth-password=xxxxxx
set smtp-auth=login
会报错：
s-nail: Warning: variable superseded or obsoleted: smtp
s-nail: Warning: variable superseded or obsoleted: smtp-auth-user
s-nail: Warning: variable superseded or obsoleted: smtp-auth-password
s-nail: Obsoletion warning: please do not use *smtp*, instead assign a smtp:// URL to *mta*!
s-nail: Obsoletion warning: Use of old-style credentials, which will vanish in v15!
s-nail:   Please read the manual section "On URL syntax and credential lookup"
s-nail: SMTP server: 530 Login fail. A secure connection is requiered(such as ssl). More information at https://help.mail.qq.com/detail/0/1010
````
````bash
这里使用新的配置
vim /etc/s-nail.rc
### 最下面添加,这里用的163邮箱
 
# 使用 v15.0 兼容模式
set v15-compat
set from=xxxxx@163.com
# 这里格式为 mta=smtps://USER:PASSWORD@smtp.163.com
# USER(一般为自己的邮箱) 和 PASSWORD(授权码) 需要urlcodec enc编码
set mta=smtps://xxxxx%40163.com:xxxxxxxxx@smtp.163.com
# 请求严格的 TLL 传输层安全检查
set smtp-use-starttls
set smtp-auth=login
````

````bash
测试
echo "这是内容" | mail或s-nail -s "这是主题"  另一个用来接收的邮箱
````
