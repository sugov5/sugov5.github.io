# CentOS下使用SS实现科学上网


- [CentOS下使用SS实现科学上网](#centos下使用ss实现科学上网)
    - [安装 Shadowsocks Server ( Python )](#安装-shadowsocks-server--python-)
    - [配置 Shadowsocks Server](#配置-shadowsocks-server)
        - [创建配置文件](#创建配置文件)
        - [多账号配置](#多账号配置)
        - [Shadowsocks Server 常用命令](#shadowsocks-server-常用命令)
        - [设置开机启动](#设置开机启动)
    - [OpenVZ下开启BBR加速](#openvz下开启bbr加速)


## 安装 Shadowsocks Server ( Python )

* 通过PyPI：

``` bash
yum update    
yum install python-setuptools && easy_install pip   
pip install git+https://github.com/shadowsocks/shadowsocks.git@master   
```

* 通过Github：

``` bash
git clone https://github.com/shadowsocks/shadowsocks.git
cd shadowsocks
python setup.py
``` 

* 如果没有安装Git，可以直接下载Github上的ZIP包：

``` bash
wget https://github.com/shadowsocks/shadowsocks/archive/master.zip 
unzip master.zip
python setup.py install
```

## 配置 Shadowsocks Server

### 创建配置文件
> vim /etc/shadowsocks.json

``` json
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false, 
    "workers": 1    
}
```

配置文件中各个参数的含义：

* server：服务器IP地址 (IPv4/IPv6)
* server_port：服务器监听的端口
* local_address：本地监听IP地址
* local_port：本地监听端口
* password：设置密码，自定义
* timeout：超时时间（秒）
* method：加密方法，可选择 “aes-256-cfb”, “rc4-md5”等等。默认“aes-256-cfb”，无特殊要求可保持默认。参考：[Encryption](https://github.com/shadowsocks/shadowsocks/wiki/Encryption)
* fast_open：true/false。服务器Linux内核3.7+，可以开启 fast_open 以降低延迟。参考：[TCP_FASTOPEN](https://github.com/shadowsocks/shadowsocks/wiki/TCP-Fast-Open)
* workers：workers数量，默认为 1。

### 多账号配置

``` json
{
  "server":"my_server_ip",
  "port_password":{
    "8381":"pass1",
    "8382":"pass2",
    "8383":"pass3",
    "8384":"pass4"
  },
  "timeout":60,
  "method":"aes-256-cfb",
  "fast_open":false,
  "workers":1
}
``` 

### Shadowsocks Server 常用命令

``` bash
// 启动
ssserver -c /etc/shadowsocks.json -d start   

// 停止
ssserver -c /etc/shadowsocks.json -d stop

// 重启
ssserver -c /etc/shadowsocks.json -d restart

// 查看帮助
ssserver -h

// 查看日志
less /var/log/shadowsocks.log
```

### 设置开机启动

``` bash
vi /etc/rc.local

// 下面代码复制到 rc.local 中
ssserver -c /etc/shadowsocks.json -d start
```

## OpenVZ下开启BBR加速

* 安装 LKL 一键包命令

> wget --no-check-certificate https://github.com/91yun/uml/raw/master/lkl/install.sh && bash install.sh

* 判断是否安装成功

> ping 10.0.0.2

* 修改 Shadowsocks Server 监听IP：

> vi /etc/shadowsocks.json

``` json
{
    // 将server监听IP改为 0.0.0.0
    "server":"0.0.0.0",
    // 将server_port改为 haproxy 转发范围内，默认9000~9999
    "server_port":9119,
    "local_address": "0.0.0.0",
    "local_port":9999,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false, 
    "workers": 1    
}
```
___注意：需要确认VPS的 TUN/TAP 选项是否已开启___
---   

参考：

['官网'](http://shadowsocks.org)

[搭建Shadowsocks服务器.md](https://github.com/jaywcjlove/handbook/blob/master/CentOS/%E6%90%AD%E5%BB%BAShadowsocks%E6%9C%8D%E5%8A%A1%E5%99%A8.md)

[CentOS 7.0 安装部署Shadowsocks服务器。 搭建自己的VPN](https://my.oschina.net/kefy/blog/1829495)

[SS/SSR 简介](https://congcong0806.github.io/2018/04/20/SS/)

[OpenVZ架构VPS安装BBR的五种方法（UML/LKL/LKL_Rinetd）](https://www.cokemine.com/openvzbbr.html)

[OpenVZ使用BBR新姿势：LKL一键安装包（比UML简单）](https://www.91yun.co/archives/6281)