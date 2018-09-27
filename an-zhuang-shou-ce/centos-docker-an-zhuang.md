# _CentOS Docker 安装_

Docker支持以下的CentOS版本：

* CentOS 7 \(64-bit\)
* CentOS 6.5 \(64-bit\) 或更高的版本

## 前提条件

目前，CentOS 仅发行版本中的内核支持 Docker。

Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。

Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。

## 使用 yum 安装（CentOS 7下）

Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

通过

**uname -r**

命令查看你当前的内核版本

```bash
[root@runoob ~]# uname -r 3.10.0-327.el7.x86_64
```



