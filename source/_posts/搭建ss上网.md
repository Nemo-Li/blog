---
title: 快速搭建SS
---

#### shell远程登录服务器

1. vultr服务器创建完毕

```Shell
ssh root@自己的ip地址
```

2. 获取安装脚本，并修改成可执行文件

```Shell
wget –no-check-certificate  https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh
```

```Shell
chmod +x shadowsocks.sh
```

3. 执行脚本安装服务端shadowsocks

```Shell
./shadowsocks.sh 2>&1 | tee shadowsocks.log
```

可以直接默认下一步，最终会显示最后的账号密码等信息



#### 安装BBR加速

```shell
1. wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
2. chmod +x bbr.sh
3. ./bbr.sh
```