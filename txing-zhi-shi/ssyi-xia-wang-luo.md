搭建shadowsocks服务端的前提是你应该有一个服务器，推荐一下[DigitalOcean](https://m.do.co/c/e71526d11cd5)。创建服务器的教程详见：[如何在Digitalocean上构建一个服务器](https://www.itfeichai.com/create-a-dg-account-and-connect-droplets)；



这里介绍的是使用Pip作为管理软件对shadowsocks进行管理，所以我们应该首先安装一下pip。

## 安装pip

pip的安装这里参考[官网-Installation](https://pip.pypa.io/en/stable/installing/),即，输入`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`后回车，如下：

```bash
[root@ssserver ~]# curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1604k  100 1604k    0     0  11.1M      0 --:--:-- --:--:-- --:--:-- 11.2M
```

然后，输入`python get-pip.py`之后回车，如下：

```bash
[root@ssserver ~]# python get-pip.py
Collecting pip
  Downloading https://files.pythonhosted.org/packages/5f/25/e52d3f31441505a5f3af41213346e5b6c221c9e086a166f3703d2ddaf940/pip-18.0-py2.py3-none-any.whl (1.3MB)
    100% |████████████████████████████████| 1.3MB 11.3MB/s 
Collecting wheel
  Downloading https://files.pythonhosted.org/packages/81/30/e935244ca6165187ae8be876b6316ae201b71485538ffac1d718843025a9/wheel-0.31.1-py2.py3-none-any.whl (41kB)
    100% |████████████████████████████████| 51kB 17.5MB/s 
Installing collected packages: pip, wheel
Successfully installed pip-18.0 wheel-0.31.1
[root@ssserver ~]#  
```

## 安装shadowsocks

输入`pip install shadowsocks`后回车，如下：

```bash
[root@ssserver ~]# pip install shadowsocks
Collecting shadowsocks
  Downloading https://files.pythonhosted.org/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
Building wheels for collected packages: shadowsocks
  Running setup.py bdist_wheel for shadowsocks ... done
  Stored in directory: /root/.cache/pip/wheels/5e/8d/b6/3e2243a7e116984b2c3597c122c29abcfeac77daa260079e88
Successfully built shadowsocks
Installing collected packages: shadowsocks
Successfully installed shadowsocks-2.8.2
```

提示安装成功！

## 配置shadowsocks

输入编辑文件命令`vi /etc/shadowsocks.json`并回车，如下：

```bash
[root@ssserver ~]# vi /etc/shadowsocks.json
```

上述步骤是编辑一个新文件，按键盘`i`键后，粘贴下面内容：

```
{
    "server":"0.0.0.0",
    "server_port":50013,
    "local_port":1080,
    "password":"1234567890",
    "timeout":600,
    "method":"aes-256-cfb"
}
```

然后按键盘’Esc’键，再按`shift+:`键，再输入`wq`并回车。文件编辑结束。

上面的50013是你的服务器端口，1234567890是你进行连接的密码。

## 将shadowsocks加入系统服务

输入编辑文件命令`vi /etc/systemd/system/shadowsocks.service`并回车，如下：

```
[root@ssserver ~]# vi /etc/systemd/system/shadowsocks.service
```

按键盘`i`键后，粘贴下面内容：

```
[Unit]
Description=Shadowsocks
[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json
[Install]
WantedBy=multi-user.target
```

然后按键盘’Esc’键，再按`shift+:`键，再输入`wq`并回车。文件编辑结束。

## 启动shadowsocks服务并设置开机自启

```
# 设置开机自启命令

systemctl enable shadowsocks


# 启动命令

systemctl start shadowsocks


#查看状态命令

systemctl status shadowsocks
```

依次执行上面的三条命令，如下：

```
[root@ssserver ~]# vi /etc/shadowsocks.json
[root@ssserver ~]# 
[root@ssserver ~]# 
[root@ssserver ~]# vi /etc/systemd/system/shadowsocks.service
[root@ssserver ~]# 
[root@ssserver ~]# 
[root@ssserver ~]# 
[root@ssserver ~]# systemctl enable shadowsocks
Created symlink from /etc/systemd/system/multi-user.target.wants/shadowsocks.service to /etc/systemd/system/shadowsocks.service.
[root@ssserver ~]# systemctl start shadowsocks
[root@ssserver ~]# systemctl status shadowsocks
● shadowsocks.service - Shadowsocks
   Loaded: loaded (/etc/systemd/system/shadowsocks.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2018-08-28 13:27:53 UTC; 7s ago
 Main PID: 1259 (ssserver)
   CGroup: /system.slice/shadowsocks.service
           └─1259 /usr/bin/python /usr/bin/ssserver -c /etc/shadowsocks.json

Aug 28 13:27:53 ssserver systemd[1]: Started Shadowsocks.
Aug 28 13:27:53 ssserver systemd[1]: Starting Shadowsocks...
Aug 28 13:27:54 ssserver ssserver[1259]: INFO: loading config from /etc/shadowsocks.json
Aug 28 13:27:54 ssserver ssserver[1259]: 2018-08-28 13:27:54 INFO     loading libcrypto from libcrypto.so.10
Aug 28 13:27:54 ssserver ssserver[1259]: 2018-08-28 13:27:54 INFO     starting server at 0.0.0.0:50013
```

这样shadowsocks服务端就安装并启动成功，接下来进行客户端的连接使用就可以了，客户端下载地址：[安卓/Android/Windows/IOS/苹果手机 Shadowsocks最新版下载地址](https://www.itfeichai.com/android-windows-shadowsocks/)。

## 端口开放

有些CENTOS执行上述操作，虽然shadowsocks运行起来了，但是仍然无法访问，极大可能是防火墙没有开放端口，具体端口开放的方法详见：[CentOS开放端口的方法](https://www.itfeichai.com/centos-open-porter/)。

参考：https://blog.csdn.net/liumiaocn/article/details/80011742 ；

