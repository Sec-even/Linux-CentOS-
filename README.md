# Linux-CentOS-
Linux(CentOS)安全加固之非业务端口服务关闭
Linux安全加固

提要：CentOS 系统默认可能开启了一些非业务服务端口，处于安全考虑我们可以将这些非必要服务进行关闭，本编文件简单记录了具体的操作流程。

场景： 本编文档，以关闭TCP 25 端口对应的服务为目标，展开案例配置。
1、查找端口对应的服务进程
[root@localhost ~]# netstat -ntlp|grep 25

[root@usm postfix]# netstat -ntlp |grep 25
tcp        0      0 0.0.0.0:37138               0.0.0.0:*                   LISTEN      2542/rpc.statd      
tcp        0      0 127.0.0.1:25                0.0.0.0:*                   LISTEN      15903/master        
tcp        0      0 ::1:25                      :::*                        LISTEN      15903/master        
tcp        0      0 :::47976                    :::*                        LISTEN      2542/rpc.statd 
2、查找进程对应的服务
由以上内容，我们可以看到TCP 25 端口对应的服务为master,那么接下来我们继续查找master 进程对应的具体服务名。

（1）查找master的存放位置

[root@usm ~]# locate master |grep '/master$'

/usr/libexec/postfix/master
[root@usm ~]# 
（2）查询master文件的安装包名称

[root@usm init.d]# rpm -qf '/usr/libexec/postfix/master'

postfix-2.6.6-2.2.el6_1.x86_64
（3）查找postfix服务的启动文件

[root@usm ~]# ll /etc/init.d/postfix

-rwxr-xr-x. 1 root root 3852 12月  3 2011 /etc/init.d/postfix
依据master安装包的名称是postfix,我们可以推知master 进程对应的服务启动文件是 
/etc/init.d/postfix。

3、停用进程服务
在查到TCP 25端口对应的服务为postfix后，接下来我们需要完成两个配置。

停用postfix服务
禁用postfix服务（off）
（1）停止postfix服务
[root@usm ~]# /etc/init.d/postfix stop 
或者
[root@usm ~]# service postfix stop
（2）设置postfix默认启动配置为off
[root@usm ~]# chkconfig --level 3 postfix off
postfix服务停用具体配置过程
[root@usm ~]# chkconfig --list |grep postfix
postfix         0:关闭    1:关闭    2:启用    3:启用    4:启用    5:启用    6:关闭
[root@usm ~]# chkconfig --level 3 postfix off
[root@usm ~]# chkconfig --list|grep postfix
postfix         0:关闭    1:关闭    2:启用    3:关闭    4:启用    5:启用    6:关闭
[root@usm ~]# 
4、学习小结
本篇文档旨在通过“PORT”(端口)来查找linux下对应的具体服务，然后关闭服务的操作过程。具体操作过程总结下，具体思路如下。

（1）定位非业务端口

（2）依据端口找到对应的服务进程名；

（3）依据进程名，我们查找进程名的文件位置；

（4）依据进程文件，我们来查找具体的软件装包，查找到了安装包，也就找到了具体的其具体的服务名； 
（5）最后是进服务的停用与禁用操作。
