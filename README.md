# Transmission-2.94_skiphashchek

这是基于 [Transmission 2.94](https://github.com/transmission/transmission/tree/2.94) 源码同时仿照 [superlukia/transmission-2.92_skiphashcheck](https://github.com/superlukia/transmission-2.92_skiphashcheck) 制作而成的可跳过校验的修改版。请注意:**使用修改版客户端 跳过校验 可能会导致您的账号被封禁 ，请你务必了解 跳过校验 后带来的风险**。

正在使用手机浏览本页面的用户 [请点击这里](https://github.com/blackyau/Transmission_SkipHashChek/blob/master/README.md)，查看完整的说明文档。

## 下载编译

[想要手动修改的看这里(较复杂)](https://github.com/blackyau/Transmission_SkipHashChek/blob/master/ModifyCodeYourself.md)

### 安装依赖

#### CentOS 7

```Shell
yum install gcc gcc-c++ m4 make automake libtool gettext openssl-devel libcurl-devel libevent-devel intltool gtk3-devel wget vim zip unzip
```

#### Ubuntu/Debian 18.04
```Shell
apt-get install -y build-essential automake autoconf libtool pkg-config intltool libcurl4-openssl-dev libglib2.0-dev libevent-dev libminiupnpc-dev libgtk-3-dev libappindicator3-dev ca-certificates libssl-dev pkg-config cmake openssl libssl1.0-dev zip unzip vim
```

### 下载源码

```Shell
wget https://github.com/blackyau/Transmission_SkipHashChek/archive/2.94_mod.zip
```

### 解压编译

```Shell
unzip Transmission_SkipHashChek-2.94_mod.zip
cd Transmission_SkipHashChek-2.94_mod
chmod +x autogen.sh
./autogen.sh
make
sudo make install
```

## 如何配置

在你编译安装完毕后，还需要一定的配置才能够使用。请注意:以下配置只适用于 PT([Private Tracker](https://www.baidu.com/s?wd=pt%E4%B8%8B%E8%BD%BD)) 不适用于 BT 。

### 创建启动脚本

```Shell
sudo vim /etc/systemd/system/transmission.service
写入以下内容
[Unit]
Description=Transmission BitTorrent Daemon
After=network.target
 
[Service]
User=root
LimitNOFILE=100000
ExecStart=/usr/local/bin/transmission-daemon -f --log-error -g /usr/local/transmission
 
[Install]
WantedBy=multi-user.target
```

### 设置脚本权限并设置开机自启

```Shell
sudo chmod +x /etc/systemd/system/transmission.service
systemctl daemon-reload
systemctl enable transmission
```

### 启动 Transmission 生成默认配置文件

```Shell
systemctl start transmission.service
```

### 关闭 Transmission 否则配置文件修改不会生效

```Shell
systemctl stop transmission.service
```

### 修改配置文件

```Shell
sudo vim /usr/local/transmission/settings.json
```

### 根据自己情况修改以下选项

```json
"cache-size-mb": 512, #缓存大小，单位MB，建议设置内存大小的1/6~1/4
"dht-enabled": false, #启用DHT网络（通过tracker寻找节点）
"download-dir": "/var/lib/transmission/Downloads",  #下载完后文件存放目录
"incomplete-dir": "/var/lib/transmission/Downloads",  #正在下载的文件目录
"incomplete-dir-enabled": true, #启用正在下载的文件的保存路径
"peer-limit-global": 100000, #全局种子最大连接数
"peer-limit-per-torrent": 100, #每个种子最多连接数(根据你的带宽自行决定)
"peer-port": 51413, #传入端口号(建议调成和默认的不一样 10000-65535 皆可)
"pex-enabled": false, #节点交换
"preallocation": 0, #预分配文件磁盘空间，0=关闭，1=快速，2=完全，默认取1这里选0是为了能够快速完成下载
"rpc-authentication-required": true, #远程连接密码验证 不开的话谁都可以进后台
"rpc-password": "password", #这里是远程连接的密码 保存配置文件后这里会被加密不显示明文
"rpc-port": 9091, #网页GUI的端口
"rpc-username": "whsir",#远程连接的用户名
"rpc-whitelist-enabled": false,  #这里一定要禁用白名单 启用的话就只有在白名单里面的 ip 才能访问网页
"script-torrent-done-enabled": false, #在torrent完成时运行脚本，默认关闭
"script-torrent-done-filename": "", #脚本路径
"speed-limit-down": 100, #下载速度限制默认100KB/s
"speed-limit-down-enabled": false,#启用下载速度限制。默认关闭
"speed-limit-up": 100, #上传速度限制，默认100KB/s
"speed-limit-up-enabled": false, #启用上传速度限制。默认关闭
"start-added-torrents": true,#添加种子文件后，自动开始，如果为false，添加种子后不会自动开始
"trash-original-torrent-files": false,#是否删除监控目录添加的种子文件，也就是说在watch-dir监控的目录下添加种子文件后，任务开始后会自动删除添加的种子文件。watch-dir需要手动添加，在最下面。
"upload-slots-per-torrent": 30,#每个种子上传连接数(根据你的带宽自行决定)
"utp-enabled": true #UTP传输是否启用
#下面两个需要手动添加的选项，注意每行配置参数都是以逗号结尾，最后一行参数没有逗号（添加下面的参数一定要注意上面最后一行要以逗号结尾，例如"utp-enabled": true,）
#"watch-dir": "/root/test", #自动监控种子目录，将种子文件下载或放在此文件夹下，会自动开始下载文件
#"watch-dir-enabled": true #是否开启自动监控种子目录
```

配置文件中的更多参数设置可参考:

[GitHub - 官方wiki (英语)](https://github.com/transmission/transmission/wiki/Editing-Configuration-Files)

[吴昊博客 - Transmission 2.92 配置文件参数中文解释](https://blog.whsir.com/post-1182.html)

[360doc - 超详细的Transmission 2.31 |迅雷离线PT下载交流](http://www.360doc.com/content/14/0311/08/15024809_359678743.shtml)

如果你懒得一个一个调，这是我推荐的完整设置。对于一个100M的盒子来说，应该比较合适。

```json
{
    "alt-speed-down": 50,
    "alt-speed-enabled": false,
    "alt-speed-time-begin": 540,
    "alt-speed-time-day": 127,
    "alt-speed-time-enabled": false,
    "alt-speed-time-end": 1020,
    "alt-speed-up": 50,
    "bind-address-ipv4": "0.0.0.0",
    "bind-address-ipv6": "::",
    "blocklist-enabled": false,
    "blocklist-url": "http://www.example.com/blocklist",
    "cache-size-mb": 512,#缓存大小 单位MB,建议设置内存大小的1/6~1/4.如果你在同时运行多个PT程序,请酌情调低.
    "dht-enabled": false,
    "download-dir": "/root/Downloads",
    "download-queue-enabled": true,
    "download-queue-size": 3,
    "encryption": 1,
    "idle-seeding-limit": 30,
    "idle-seeding-limit-enabled": false,
    "incomplete-dir": "/root/Downloads",
    "incomplete-dir-enabled": true,
    "lpd-enabled": false,
    "message-level": 1,
    "peer-congestion-algorithm": "",
    "peer-id-ttl-hours": 6,
    "peer-limit-global": 100000,
    "peer-limit-per-torrent": 100,
    "peer-port": 51413,
    "peer-port-random-high": 65535,
    "peer-port-random-low": 49152,
    "peer-port-random-on-start": false,
    "peer-socket-tos": "default",
    "pex-enabled": false,
    "port-forwarding-enabled": true,
    "preallocation": 0,
    "prefetch-enabled": true,
    "queue-stalled-enabled": true,
    "queue-stalled-minutes": 30,
    "ratio-limit": 2,
    "ratio-limit-enabled": false,
    "rename-partial-files": true,
    "rpc-authentication-required": true,
    "rpc-bind-address": "0.0.0.0",
    "rpc-enabled": true,
    "rpc-host-whitelist": "",
    "rpc-host-whitelist-enabled": true,
    "rpc-password": "kJrk15kLooWBzP3", #这里是远程连接的密码,保存配置文件后这里会被加密不显示明文
    "rpc-port": 9091, #网页GUI的端口
    "rpc-url": "/transmission/",
    "rpc-username": "user", #远程连接的用户名
    "rpc-whitelist": "127.0.0.1",
    "rpc-whitelist-enabled": false,
    "scrape-paused-torrents-enabled": true,
    "script-torrent-done-enabled": false,
    "script-torrent-done-filename": "",
    "seed-queue-enabled": false,
    "seed-queue-size": 10,
    "speed-limit-down": 100,
    "speed-limit-down-enabled": false,
    "speed-limit-up": 100,
    "speed-limit-up-enabled": false,
    "start-added-torrents": true,
    "trash-original-torrent-files": false,
    "umask": 18,
    "upload-slots-per-torrent": 50,
    "utp-enabled": true
}
```

### 启动 Transmission

```Shell
systemctl start transmission.service
```

### 开启防火墙端口

**下面开启的端口分别为上面配置中的 rpc-port 和 peer-port 如果自己修改过配置，也依葫芦画瓢改改命令**

CentOS 7:

```Shell
firewall-cmd --permanent --add-port=9091/tcp --add-port=51413/tcp --add-port=51413/udp
firewall-cmd --reload
```

Ubuntu 18.04:

```Shell
sudo ufw allow 9091
sudo ufw allow 51413
```

### 安装增强 Transmission GUI

如果安装有问题或不会请移步至 [https://github.com/ronggang/transmission-web-control](https://github.com/ronggang/transmission-web-control)

```Shell
wget -N https://github.com/ronggang/transmission-web-control/raw/master/release/install-tr-control-cn.sh --no-check-certificate
chmod +x install-tr-control-cn.sh
bash install-tr-control-cn.sh
```

### 如何跳过校验

打开网页GUI `http://vps 的 ip:9091` 
在使用增强 Transmission GUI 的环境下,你只需要在左边勾选(只能单选)你需要跳过校验的种子,然后点击 获取更多Peer 即可跳过校验。
在原版 Transmission GUI 的环境下你可以右键任意种子，然后点击 Ask tracker for more peers 即可跳过校验。

## 参考

[GitHub - transmission/transmission](https://github.com/transmission/transmission)

[GitHub - superlukia/transmission-2.92_skiphashcheck](https://github.com/superlukia/transmission-2.92_skiphashcheck)

[GitHub - transmission Wiki Building Transmission](https://github.com/transmission/transmission/wiki/Building-Transmission)

[GitHub - transmission Wiki Editing Configuration Files](https://github.com/transmission/transmission/wiki/Editing-Configuration-Files)

[一曲长歌辞烟雨 - CentOS编译安装Transmission](https://archive.ls12.me/centos-install-transmission.html)

[吴昊博客 - Transmission2.94最新版完整编译安装并汉化](https://blog.whsir.com/post-2881.html)

[吴昊博客 - Transmission2.92配置文件参数中文解释](https://blog.whsir.com/post-1182.html)

[360doc - 超详细的Transmission 2.31 |迅雷离线PT下载交流](http://www.360doc.com/content/14/0311/08/15024809_359678743.shtml)

[91yun - 在VPS上离线下载PT/BT/磁力链:CentOS下transmission安装教程](https://www.91yun.co/archives/31861)

[简书 - 源码编译安装Transmission 2.93（debian 7)](https://www.jianshu.com/p/551ed5464e81)