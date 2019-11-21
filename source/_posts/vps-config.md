---
title: VPS配置
date: 2018-03-06 18:25:30
tags: Ubuntu
---

## 修改主机名称
``` shell
vim /etc/hostname
```

## 创建用户
``` shell
useradd -m name
passwd name
chsh -s /bin/bash name

# 添加权限
vim /etc/sudoers
name ALL=(ALL:ALL) ALL
```

## ssh 登陆并禁止密码登陆
``` shell
mkdir .ssh
cd .ssh

vim authorized_keys
# 输入公钥
sudo chmod 600 authorized_keys
```

修改 ssh 配置
``` shell
sudo vim /etc/ssh/sshd_config
```

修改 ```Port```  
修改 ```PermitRootLogin yes``` 为 ```PermitRootLogin no```  
修改 ```PasswordAuthentication yes``` 为 ```PasswordAuthentication no```  

``` shell
sudo service ssh reload
```

## 添加防火墙
``` shell
ufw default deny

# 添加允许访问的端口
ufw allow 80

# 启动
ufw enable
```

## 更新
``` shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get remove
sudo apt-get purge
sudo apt-get autoremove
sudo apt-get autoclean
```
