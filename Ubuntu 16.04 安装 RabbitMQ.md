Ubuntu 16.04 安装 RabbitMQ

#1 更新
$ sudo apt-get update
$ sudo apt-get upgrade

#2 安装Erlang
$ cd /tmp

添加密钥：
$ wget http://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc
$ sudo apt-key add erlang_solutions.asc
$ sudo apt-get update

$ sudo apt-get install erlang
$ sudo apt-get install erlang-nox

#3 安装RabbitMQ
添加RabbitMQ仓库源：
$ sudo vim /etc/apt/sources.list
在文件中添加一行：
deb http://www.rabbitmq.com/debian/ testing main

添加密钥：
$ cd /tmp
$ wget https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
$ sudo apt-key add rabbitmq-signing-key-public.asc

安装rabbitmq-server：
$ sudo apt-get update
$ sudo apt-get install rabbitmq-server

启动RabbitMQ服务：
$ sudo systemctl enable rabbitmq-server
$ sudo systemctl start rabbitmq-server


查看RabbitMQ服务状态：
$ sudo systemctl status rabbitmq-server


#4 RabbitMQ web管理接口

启用rabbitmq-management插件：
$ sudo rabbitmq-plugins enable rabbitmq_management
重启RabbitMQ：

$ sudo systemctl restart rabbitmq-server
使用浏览器访问 http://192.168.217.128:15672/，使用默认的guest/guest用户登录：



-------------------
检查rabbit状态
rabbitmqctl status


rabbitMQ 开启远程访问功能：

出于安全的考虑，guest这个默认的用户只能通过http://localhost:15672 来登录，
其他的IP无法直接使用这个账号。 这对于服务器上没有安装桌面的情况是无法管理维护的，
除非通过在前面添加一层代理向外提供服务，这个又有些麻烦了，这里通过配置文件来实现这个功能。

1.添加远程登录用户：
$ sudo rabbitmqctl add_user huang 111111
$ sudo rabbitmqctl change_password huang 111111

$ sudo rabbitmqctl add_user huangbaobao 111111

2.添加用户administrator权限
$ sudo rabbitmqctl set_user_tags huangbaobao administrator 
【
设置用户角色的命令为：
rabbitmqctl  set_user_tags  User  Tag
User为用户名， Tag为角色名(对应于上面的administrator，monitoring，policymaker，management，或其他自定义名称)。
】
  
3.添加主机权限
$ sudo rabbitmqctl set_permissions -p / huangbaobao ".*" ".*" ".*"
[该命令使用户huangbaobao具有 / 这个virtual host中所有 资源的配置、写、读权限 以便管理其中的资源]

4.启动rabbitMQ插件 来进行web端登录监控
$ sudo rabbitmq-plugins enable rabbitmq_management	

http://192.168.217.128:15672/

=================
坑：
打开web管理器
http://192.168.217.128:15672/
guest
guest
 
结果无反应 ，查看日志：
{"error":"not_authorised","reason":"User can only log in via localhost"}

解决：
rabbitmq从3.3.0开始禁止使用guest/guest权限通过除localhost外的访问。
如果想使用guest/guest通过远程机器访问，需要在rabbitmq配置文件中(/etc/rabbitmq/rabbitmq.config)中设置loopback_users为[]。

-----------------------------------------
cd /usr/share/doc/rabbitmq-server
gunzip rabbitmq.config.example.gz
cp -a rabbitmq.config.example /etc/rabbitmq/rabbitmq.config

/etc/rabbitmq/rabbitmq.config文件完整内容如下（注意后面的半角句号）：
[{rabbit, [{loopback_users, []}]}]



===================
常用命令：
循环日志文件 
# rabbitmqctl rotate_logs[suffix] 
7.集群管理  
# rabbitmqctl cluster clusternode… 
用户管理 
1.添加用户 
# rabbitmqctl add_user username password 
2.删除用户 
# rabbitmqctl delete_user username 
3.修改密码 
# rabbitmqctl change_password username newpassword 
4.列出所有用户 
# rabbitmqctl list_users 
 
权限控制 
1.创建虚拟主机 
# rabbitmqctl add_vhost vhostpath 
2.删除虚拟主机  
# rabbitmqctl delete_vhost vhostpath 
3.列出所有虚拟主机 
# rabbitmqctl list_vhosts 
4.设置用户权限 
# rabbitmqctl set_permissions [-p vhostpath] username regexp regexp regexp 
5.清除用户权限 
# rabbitmqctl clear_permissions [-p vhostpath] username 
6.列出虚拟主机上的所有权限 
# rabbitmqctl list_permissions [-p vhostpath] 
7.列出用户权限 
# rabbitmqctl list_user_permissions username

======================================
安装php扩展：
aptitude search php7.0-amqp
aptitude install php7.0-amqp


