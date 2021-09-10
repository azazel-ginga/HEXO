# Linux操作系统安全配置

请对服务器Alinux（用户名：root；密码：123456）按要求进行相应的设置，提高服务器的安全性。

1.设置最小密码长度不少于8个字符，密码最长使用期限为15天。将/etc/login.defs配置文件中对应的部分截图;

![image-20210908203758969](D:\HEXO\source\_posts\Linux操作系统安全配置\1.png)

2.设置在用户登录系统时，会有“For authorized users only”提示信息，将登录系统时系统弹出提示信息界面截图;

修改路径：/etc/issue

![image-20210908200346678](D:\HEXO\source\_posts\Linux操作系统安全配置\2.png)

3.设置root用户的计划任务。每天早上7:50自动开启vsftpd服务，22点50时关闭；每周六的7:30时，重新启动vsftpd服务。使用crontab –l 查看计划任务，将回显结果截图；

![image-20210908205809385](D:\HEXO\source\_posts\Linux操作系统安全配置\3.png)

4.禁止匿名用户登录vsftpd服务，将/etc/vsftpd/vsftpd.conf配置文件中对应的部分截图；

![image-20210908204341192](D:\HEXO\source\_posts\Linux操作系统安全配置\4.png)

5.限制客户端连接vsftpd服务时，同一个IP最多只能打开两个连接，将/etc/vsftpd/vsftpd.conf配置文件中对应的部分截图；

在vsftpd.conf配置中添加 max_per_ip=2

![image-20210908204803186](D:\HEXO\source\_posts\Linux操作系统安全配置\5.png)

6.将SSH服务的端口号修改为2222 ,使用命令netstat -anltp | grep sshd查看SSH服务端口信息，将回显结果截图；

路径/etc/ssh/sshd_config

![image-20210908205046573](D:\HEXO\source\_posts\Linux操作系统安全配置\6.png)

![image-20210908205504860](D:\HEXO\source\_posts\Linux操作系统安全配置\7.png)

7.禁止root用户通过SSH远程登录，将/etc/ssh/sshd_config 配置文件中对应的部分截图；

![image-20210908210049056](D:\HEXO\source\_posts\Linux操作系统安全配置\8.png)

8.禁止mysql服务以管理员权限的账号运行，将/etc/my.cnf配置文件中对应的部分截图；

![image-20210908210345280](D:\HEXO\source\_posts\Linux操作系统安全配置\9.png)

9.删除默认数据库test, 然后使用show databases;命令查看当前的数据库信息，将回显结果截图；

![image-20210909210316656](D:\HEXO\source\_posts\Linux操作系统安全配置\10.png)

10.将数据库用户user1的密码使用MD5加密，然后在数据库中使用select password from user where user=’user1’;命令查看user1的密码，将回显结果截图。

![image-20210909211209660](D:\HEXO\source\_posts\Linux操作系统安全配置\11.png)

