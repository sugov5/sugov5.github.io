## Github 加速
---

对于国内的网络环境已经无力吐槽。千言万语只能汇成一句：

FUCK GFW!

下面提供两种方案来加速Github的repos访问：

### 方案一：Git代理

本方案最终加速效果受限于代理服务器的速度，而用于科学上网的国外VPS速度各位可不必抱太大期望。基于此，___通常情况下请选择使用方案二___。

- Shadowsocks

    SS似乎已成为科学上网的标配，所以此处省略其服务的配置...

    SS客户端默认地址：socks5://127.0.0.1:1080

    SS默认本地代理端口为：1080，可在SS客户端的“编辑服务器”页面确认。

- 配置本地Git仅针对Github域名的代理：（***推荐***，不影响国内相关git库的访问）

    > git config --global http.https://github.com.proxy 'socks5://127.0.0.1:1080' 

- 或者，配置本地Git全局代理：

    > git config --global http.proxy 'socks5://127.0.0.1:1080' 

    > git config --global https.proxy 'socks5://127.0.0.1:1080'

- 取消代理配置

    > git config --global --unset http.https://github.com.proxy

    或 

    > git config --global --unset http.proxy

    > git config --global --unset https.proxy

### 方案二：配置本地 Hosts 文件（推荐）

访问 [https://www.ipaddress.com](https://www.ipaddress.com)，找到页面中下方的 “IP Address Tools - Quick Links”，分别输入github.global.ssl.fastly.net和github.com，查询ip地址。

在系统Hosts文件内增加github两个相关域名的解析：

```
151.101.76.249 github.global.ssl.fastly.net 
192.30.253.112 github.com
```

### 总结

不管哪种方案，都可在 ***git clone*** 时增加 ***--depth=1*** 参数，以便通过仅拉取最近一个revision来减小文件下载大小。

如果后期想要查看历史纪录，则可以：
> git fetch --unshallow

---

参考：

[git clone 太慢的解决方式](https://www.jianshu.com/p/aac268316639) 

[从此Github访问与clone不再慢了](https://www.jianshu.com/p/8cdc11ba5ff2)