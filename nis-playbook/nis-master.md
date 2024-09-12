```shell
# 安装相关软件
[root@c1 root]# yum install yp-tools ypbind ypserv -y

# 设置NIS的域名(写入文件不会立即生效)
[root@c1 ~]# nisdomainname cluster
[root@c1 ~]# echo 'NISDOMAIN=centos' >> /etc/sysconfig/network

# 设置NIS服务器启动的端口号
[root@c1 ~]# echo 'YPSERV_ARGS=-p 1011' >> /etc/sysconfig/network
# 配置访问NIS服务器的权限
[root@c1 ~]# echo "192.168.5.67/255.255.255.0 :*:*:none" >> /etc/ypserv.conf
 
# 同步到slave servers
[root@c1 ~]# sed -i "s@NOPUSH=true@NOPUSH=false@g" /var/yp/Makefile
# 添加slave从服务器的主机名
[root@c1 ~]# cat << _YPServers_ > /var/yp/ypservers
c1
c2
_YPServers_

# 修改yppasswdd服务的端口，该功能与 NIS 客户端有关，允许用户在客户端修改服务器上的密码
[root@c1 ~]# sed -i "s@YPPASSWDD_ARGS=@YPPASSWDD_ARGS='--port 1012'@g" /etc/sysconfig/yppasswdd

# 启动yppasswdd服务并设置开机启动
[root@c1 ~]# systemctl start yppasswdd.service && systemctl enable yppasswdd.service
# 启动rpcbind服务并设置开机启动
[root@c1 ~]# systemctl start rpcbind.service && systemctl enable rpcbind.service
# 启动ypserv服务并设置开机启动
[root@c1 ~]# systemctl start ypserv.service && systemctl enable ypserv.service
# 启动ypxfrd服务并设置开机启动(slaver)
[root@c1 ~]# systemctl start ypxfrd.service && systemctl enable ypxfrd.service

# 修改添加用户默认参数, 修改默认家目录
[root@c1 ~]# sed -i "s@HOME=/home@HOME=/public/home@g" /etc/default/useradd
# 添加3个用户
[root@c1 ~]# useradd -u 1001 nisuser1
[root@c1 ~]# useradd -u 1002 nisuser2
[root@c1 ~]# useradd -u 1003 nisuser3
# 设置3个用户的初始密码
[root@c1 ~]# echo meimima | passwd --stdin nisuser1
[root@c1 ~]# echo meimima | passwd --stdin nisuser2
[root@c1 ~]# echo meimima | passwd --stdin nisuser3
# 转化帐号为数据库
[root@c1 ~]# /usr/lib64/yp/ypinit -m
	next host to add:  c1
	next host to add:  c2	# 从服务器的hostname
```
