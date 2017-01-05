Ubuntu 16.04 ��װ RabbitMQ

#1 ����
$ sudo apt-get update
$ sudo apt-get upgrade

#2 ��װErlang
$ cd /tmp

�����Կ��
$ wget http://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc
$ sudo apt-key add erlang_solutions.asc
$ sudo apt-get update

$ sudo apt-get install erlang
$ sudo apt-get install erlang-nox

#3 ��װRabbitMQ
���RabbitMQ�ֿ�Դ��
$ sudo vim /etc/apt/sources.list
���ļ������һ�У�
deb http://www.rabbitmq.com/debian/ testing main

�����Կ��
$ cd /tmp
$ wget https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
$ sudo apt-key add rabbitmq-signing-key-public.asc

��װrabbitmq-server��
$ sudo apt-get update
$ sudo apt-get install rabbitmq-server

����RabbitMQ����
$ sudo systemctl enable rabbitmq-server
$ sudo systemctl start rabbitmq-server


�鿴RabbitMQ����״̬��
$ sudo systemctl status rabbitmq-server


#4 RabbitMQ web����ӿ�

����rabbitmq-management�����
$ sudo rabbitmq-plugins enable rabbitmq_management
����RabbitMQ��

$ sudo systemctl restart rabbitmq-server
ʹ����������� http://192.168.217.128:15672/��ʹ��Ĭ�ϵ�guest/guest�û���¼��



-------------------
���rabbit״̬
rabbitmqctl status


rabbitMQ ����Զ�̷��ʹ��ܣ�

���ڰ�ȫ�Ŀ��ǣ�guest���Ĭ�ϵ��û�ֻ��ͨ��http://localhost:15672 ����¼��
������IP�޷�ֱ��ʹ������˺š� ����ڷ�������û�а�װ�����������޷�����ά���ģ�
����ͨ����ǰ�����һ����������ṩ�����������Щ�鷳�ˣ�����ͨ�������ļ���ʵ��������ܡ�

1.���Զ�̵�¼�û���
$ sudo rabbitmqctl add_user huang 111111
$ sudo rabbitmqctl change_password huang 111111

$ sudo rabbitmqctl add_user huangbaobao 111111

2.����û�administratorȨ��
$ sudo rabbitmqctl set_user_tags huangbaobao administrator 
��
�����û���ɫ������Ϊ��
rabbitmqctl  set_user_tags  User  Tag
UserΪ�û����� TagΪ��ɫ��(��Ӧ�������administrator��monitoring��policymaker��management���������Զ�������)��
��
  
3.�������Ȩ��
$ sudo rabbitmqctl set_permissions -p / huangbaobao ".*" ".*" ".*"
[������ʹ�û�huangbaobao���� / ���virtual host������ ��Դ�����á�д����Ȩ�� �Ա�������е���Դ]

4.����rabbitMQ��� ������web�˵�¼���
$ sudo rabbitmq-plugins enable rabbitmq_management	

http://192.168.217.128:15672/

=================
�ӣ�
��web������
http://192.168.217.128:15672/
guest
guest
 
����޷�Ӧ ���鿴��־��
{"error":"not_authorised","reason":"User can only log in via localhost"}

�����
rabbitmq��3.3.0��ʼ��ֹʹ��guest/guestȨ��ͨ����localhost��ķ��ʡ�
�����ʹ��guest/guestͨ��Զ�̻������ʣ���Ҫ��rabbitmq�����ļ���(/etc/rabbitmq/rabbitmq.config)������loopback_usersΪ[]��

-----------------------------------------
cd /usr/share/doc/rabbitmq-server
gunzip rabbitmq.config.example.gz
cp -a rabbitmq.config.example /etc/rabbitmq/rabbitmq.config

/etc/rabbitmq/rabbitmq.config�ļ������������£�ע�����İ�Ǿ�ţ���
[{rabbit, [{loopback_users, []}]}]



===================
�������
ѭ����־�ļ� 
# rabbitmqctl rotate_logs[suffix] 
7.��Ⱥ����  
# rabbitmqctl cluster clusternode�� 
�û����� 
1.����û� 
# rabbitmqctl add_user username password 
2.ɾ���û� 
# rabbitmqctl delete_user username 
3.�޸����� 
# rabbitmqctl change_password username newpassword 
4.�г������û� 
# rabbitmqctl list_users 
 
Ȩ�޿��� 
1.������������ 
# rabbitmqctl add_vhost vhostpath 
2.ɾ����������  
# rabbitmqctl delete_vhost vhostpath 
3.�г������������� 
# rabbitmqctl list_vhosts 
4.�����û�Ȩ�� 
# rabbitmqctl set_permissions [-p vhostpath] username regexp regexp regexp 
5.����û�Ȩ�� 
# rabbitmqctl clear_permissions [-p vhostpath] username 
6.�г����������ϵ�����Ȩ�� 
# rabbitmqctl list_permissions [-p vhostpath] 
7.�г��û�Ȩ�� 
# rabbitmqctl list_user_permissions username

======================================
��װphp��չ��
aptitude search php7.0-amqp
aptitude install php7.0-amqp


