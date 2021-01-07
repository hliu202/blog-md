---
title: Globalprotect - a problem with the security certificate 
date: 2020-04-22 15:34:08
categories: tools
tags: bug
---

最近遇到个很迷的 bug，导致 globalprotect 不能连上公司 VPN，错误信息是关于 `Security Certificate`，详情如下：

<!--more-->

```bash
❯ globalprotect connect -p vpn-xx.com
Retrieving configuration...

There is a problem with the security certificate, so the identity of den-vpn.amperecomputing.com cannot be verified. Please contact the Help Desk for your organization to have the issue rectified.
Warning: The communication with vpn-xx.com may have been compromised. We recommend that you do not continue with this connection.
```

网上找了好久，没有找到直接的解决方案。后来 Stackoverflow 上找到个 `certificate` 相关的问题：[How does one remove a certificate authority's certificate from a system?](https://askubuntu.com/questions/440580/how-does-one-remove-a-certificate-authoritys-certificate-from-a-system)，照着做了第一步：

```bash
❯ sudo dpkg-reconfigure ca-certificates
/usr/sbin/dpkg-reconfigure: ca-certificates is broken or not fully installed
```

报错了，才发现应该是 `ca-certificates` 问题，重新安装之后就好了

```bash
❯ sudo apt-get install ca-certificates
```
