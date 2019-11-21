---
title: Zerotier 安装配置
date: 2019-08-20 12:25:15
tags: Zerotier
---

## 安装

``` bash
curl -s https://install.zerotier.com/ | sudo bash
```

ZeroTier 目录位置：

* Windows `C:\ProgramData\ZeroTier\One`
* Macintosh `/Library/Application Support/ZeroTier/One`
* Linux `/var/lib/zerotier-one`
* FreeBSD/OpenBSD `/var/db/zerotier-one`

## Moon

修改默认端口
``` bash
sudo ufw allow 12345
sudo vim local.conf

{
  "settings":
  {
      "primaryPort": 12345
  }
}
```

生成 moon.json
``` bash
zerotier-idtool initmoon /var/lib/zerotier-one/identity.public >>moon.json
```

修改 stableEndpoints

``` bash
"stableEndpoints": [ "1.2.3.4/12345","abcd:abcd:abcd::1/12345" ]
```

生成签名文件
``` bash
zerotier-idtool genmoon moon.json
```

移动到 ZeroTier 目录
``` bash
cd /var/lib/zerotier-one
sudo mkdir moons.d
sudo cp ~/software/zerotier/0000000000000000.moon moons.d/
```

重新启动
``` bash
sudo systemctl stop zerotier-one.service
sudo systemctl start zerotier-one.service
sudo systemctl status zerotier-one.service
```

查看状态
``` bash
sudo zerotier-cli info
sudo zerotier-cli listpeers
sudo zerotier-cli listmoons
sudo zerotier-cli listnetworks
```
