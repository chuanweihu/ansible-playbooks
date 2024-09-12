
```shell
# 设置主机名与IP的对应
[root@c1 ~]# cat << _ServerHost_ >> /etc/hosts
192.168.5.67  c1
192.168.5.133 c2
192.168.5.119 c3
_ServerHost_


# 配置分享的文件系统
[root@c1 ~]# echo "/public 192.168.5.133(rw,sync,no_root_squash) 192.168.5.119(rw,sync,no_root_squash)" >> /etc/exports
# 安装相关软件
[root@c1 ~]# yum install -y nfs-utils rpchbind
# 启动rpcbind服务并设置开机启动
[root@c1 ~]# systemctl start rpcbind && systemctl enable rpcbind
# 启动nfs服务并设置开机启动
[root@c1 ~]# systemctl start nfs && systemctl enable nfs
# 显示设定好的相关 exports 分享目录信息
[root@c1 ~]# showmount -e c1
```