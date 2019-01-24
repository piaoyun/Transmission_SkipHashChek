# Transmission-2.94_skiphashchek

这是基于 [Transmission 2.94](https://github.com/transmission/transmission/tree/2.94) 源码同时仿照 [superlukia/transmission-2.92_skiphashcheck](https://github.com/superlukia/transmission-2.92_skiphashcheck) 制作而成的可跳过校验的修改版。请注意:**使用修改版客户端 跳过校验 可能会导致您的账号被封禁 ，请你务必了解 跳过校验 后带来的风险**。

更简便一些的下载编译请看这里[https://github.com/blackyau/Transmission_SkipHashChek/blob/master/README.md](https://github.com/blackyau/Transmission_SkipHashChek/blob/master/README.md)

## 手动修改

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
wget https://github.com/transmission/transmission/archive/2.94.zip
unzip transmission-2.94
cd transmission-2.94
cd libtransmission
```

### 修改源码

可以看 [commit d8e60ee](https://github.com/blackyau/Transmission_SkipHashChek/commit/0a18e4dffc7002eb80abe39ae8c8c8aec1205967) 简单易懂

rpcimpl.c

第 388 行和 390 之间添加

```c
skiphash();
```

verify.c

第 41 行和 42 行之间添加

```c
static bool skiphashcheck=false;
void skiphash(){
    skiphashcheck=true;
}
```

第 81 行修改为

```c
if (!skiphashcheck && filePos == 0 && fd == TR_BAD_SYS_FILE && fileIndex != prevFileIndex)
```

第 100 行修改为

```c
if (!skiphashcheck && tr_sys_file_read_at (fd, buffer, bytesThisPass, filePos, &numRead, NULL) && numRead > 0)
```

第 124 行修改为

```c
hasPiece = skiphashcheck || !memcmp (hash, tor->info.pieces[pieceIndex].hash, SHA_DIGEST_LENGTH);
```

第 161 行和 162 行之间添加

```c
if(skiphashcheck){
      skiphashcheck=false;
      tr_logAddTorInfo (tor, "%s", _("skip hash check"));
  }
```

### 编译安装

```Shell
cd ~/transmission-2.94
chmod +x autogen.sh
./autogen.sh
make
sudo make install
```

### 配置以及后续

[https://github.com/blackyau/Transmission_SkipHashChek/blob/master/README.md#如何配置](https://github.com/blackyau/Transmission_SkipHashChek/blob/master/README.md#如何配置)