# linux shell scripts

## 编写第一支自己的scripts
````bash
#!/bin/bash
# Progarm:
#      This program shows "Hello World!" in your screen.
# History:  
# 2025/04/10      hao2       First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "Hello World! \a \n"
exit 0
````
## 运维常用脚本

### 服务器资源监控脚本
 功能：监控CPU、内存、磁盘使用率，超过阈值发送告警。
````bash
#!/bin/bash
# Progarm:
#      This program 监控CPU、内存、磁盘使用率，超过阈值发送告警。
# History:  
# 2025/04/11      hao2       First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 定义阈值
CPU_THRESHOLD=80
MEM_THRESHOLD=90
DISK_THRESHOLD=85

# 获取CPU使用率（使用`top`命令非交互式获取）
cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}' | cut -d. -f1)

# 获取内存使用率（使用`free`命令）
mem_usage=$(free | grep Mem | awk '{print ($3/$2)*100}' | cut -d. -f1)

# 获取根分区磁盘使用率
disk_usage=$(df -h | grep '/dev/vda3' | awk '{print $5}' | tr -d '%')

# 告警逻辑
send_alert() {
    echo "WARNING: $1 usage is $2%!" | mail -s "System Alert" admin@example.com
}

# 检查CPU
if [ $cpu_usage -ge $CPU_THRESHOLD ]; then
    send_alert "CPU" $cpu_usage
fii

# 检查内存
if [ $mem_usage -ge $MEM_THRESHOLD ]; then
    send_alert "Memory" $mem_usage
fi

# 检查磁盘
if [ $disk_usage -ge $DISK_THRESHOLD ]; then
    send_alert "Disk" $disk_usage
fi
````

### 日志分析脚本
 功能：统计Nginx日志中HTTP状态码的出现次数（如404/500）
````bash
#!/bin/bash
# Progarm:
#      This program 统计Nginx日志中HTTP状态码的出现次数（如404/500）
# History:  
# 2025/04/11      hao2       First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

LOG_FILE="/var/log/nginx/access.log"
STATUS_CODES="200 404 500"

# 分析最近1小时的日志
for code in $STATUS_CODES; do
    count=$(grep $(date -d '1 hour ago' +'%d/%b/%Y:%H') $LOG_FILE | awk '{print $9}' | grep -c "^$code$")
    echo "Status $code count: $count"
done
````
