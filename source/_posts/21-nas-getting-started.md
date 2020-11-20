---
title: 折腾NAS之旅
comments: true
date: 2020-10-24 13:59
categories: [NAS]
tags: [NAS]
---

## 硬件 
> 主机预算1500以内，以后可能折腾HTPC，所以选择了i3-8100 + itx主板

#### cpu    XY
	i3-8100 
	65w   价格 628  
 	
#### 主板   XY
	华擎H310 itx  价格 430
	
#### 内存  JD
	金泰克 ddr4 2666 4G
	价格 105

#### 电源  TB
	拆机 sfx SkyDigital 
	价格  138

#### 散热器 -- 暂无
	30 超频三

#### 机箱 -- 暂无
    Mini-ITX  迷你 17 * 17
	Micro-ATX 紧凑

#### 其他
	硅脂  8元  pdd
	U盘   20元  pdd
	sata线

#### 固态硬盘  pdd
	杂牌二手 120 G   
	74 元

#### 硬盘 -- 暂无

### 装机  

收的一些破烂玩意儿，而且还是第一次装机，带着一半的把握一半的猜测把线接完了，还是没有买到合适的机箱，拿螺丝刀开机，一开始开机没成功，仔细研究了下电源跳线，原来螺丝刀短接错了地方，又试了一次，竟然点亮了，半夜里兴奋不已啊。。。。。


<!-- more -->
-------- 
## 软件

### 系统
> 选择比较熟悉的 Centos, 可以用作服务器使用

- 开始拿win10笔记本装了Centos双系统试了一下，目前没有搞定外网访问的方案，因此装了centos图形界面+向日葵远程协助，前期先使用远程桌面的方式折腾一下软件。


- 暂时没有公网ip，内网穿透改用zerotier-one <https://my.zerotier.com/network>


#### 安装系统 

切换清华源  https://link.zhihu.com/?target=https%3A//mirror.tuna.tsinghua.edu.cn/help/centos/



#### 安装docker

- 切换国内源
vi /etc/docker/daemon.json
修改
``` 
{
  "registry-mirrors": ["https://jqqhtxfc.mirror.aliyuncs.com"]
}
```

```sh
# 重启docker
sudo systemctl daemon-reload
sudo systemctl restart docker

# 设置docker自动启动
sudo systemctl enable docker.service

# 容器自动启动，增加参数
docker run --restart=always
```


- docker 安装 jellyfin
```sh
#
docker pull jellyfin/jellyfin

# 
mkdir -p /opt/jellyfin/config
mkdir -p /opt/jellyfin/cache

#  -p 8096:8096  \
docker create \
 --net=host	\
 --volume /opt/jellyfin/config:/config \
 --volume /opt/jellyfin/cache:/cache \
 --volume /media:/media \
 --restart=always \
 --name jellyfin \
 jellyfin/jellyfin

docker start jellyfin
```

**注：虚拟机无法正常使用 --net=host，绑定的是虚拟机的端口，不是物理主机**

访问 http://localhost:8096

- 安装 aria2ng
先安装 aria2-pro
```sh
docker pull p3terx/aria2-pro

#   -e RPC_SECRET=123 \
docker run -d \
  --name aria2-pro \
  --restart unless-stopped \
  --log-opt max-size=1m \
  -e PUID=$UID \
  -e PGID=$GID \

  -e RPC_PORT=6800 \
  -p 6800:6800 \
  -e LISTEN_PORT=6888 \
  -p 6888:6888 \
  -p 6888:6888/udp \
  -v /opt/docker/aria2-config:/config \
  -v /media/downloads:/downloads \
  p3terx/aria2-pro

```

```
docker pull p3terx/ariang
docker run -d \
    --name ariang \
    --restart unless-stopped \
    --log-opt max-size=1m \
	--restart=always \
    -p 6880:6880 \
    p3terx/ariang
```

访问 http://192.168.192.168:6880/

- 安装doker ui管理docker容器
```
docker pull portainer/portainer-ce
mkdir -p /opt/docker/portainer_data
docker run -d -p 8980:8000 -p 8900:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /opt/docker/portainer_data:/data portainer/portainer-ce

```
访问 http://192.168.192.168:8900/

- nextcloud
```
docker run -d \
--name nextcloud \
-p 8000:80 \
-v /data/nextcloud:/var/www/html \
nextcloud
```


- 安装 netdata(系统信息监控)
```
docker pull netdata/netdata

docker run -d --name=netdata \
  -p 19999:19999 \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  --cap-add SYS_PTRACE \
  --security-opt apparmor=unconfined \
  netdata/netdata

```  


#### samba


先从samba开始折腾。

- Centso samba 共享文件夹

安装启动samba
```sh
#------- 安装 --------
yum -y install samba samba-client samba-common
# 查看yum源中Samba版本
yum list | grep samba
# 查看samba的安装情况
rpm -qa | grep samba

#------- 启动 --------
systemctl start/stop/restart/status smb.service

# 设置smb服务开机启动
systemctl enable smb.service

```


关闭防火墙或者开启端口
```sh
firewall-cmd --zone=public --add-port=139/tcp --permanent
firewall-cmd --zone=public --add-port=389/tcp --permanent
firewall-cmd --zone=public --add-port=445/tcp --permanent
firewall-cmd --zone=public --add-port=901/tcp --permanent

firewall-cmd --reload

# 查看已经放开的端口号
firewall-cmd --list-all
```

关闭selinux
vi /etc/selinux/config
```
SELINUX=disabled
```

配置服务器
```sh
mkdir /opt/shares

# 因为需要设置匿名用户可以上传下载文件, 所以需要给shares目录授予nobody权限
chown -R nobody:nobody /opt/shares
# 访客可读，不可写， 需要可写使用 777
chmod 755 /opt/shares
```

添加用户
```sh
adduser smbtest
passwd smbtest

# 添加samba用户
smbpasswd -a smbtest

# 测试连接
smbclient //localhost/smbtest -U smbtest
```


`vi /etc/samba/smb.conf` 文件末尾添加

```sh
[public] # 其他人访问时看到的文件夹名称
        comment = Public Stuff
        path = /opt/shares  # 上边设置的共享目录
        public = yes
        #read only = no
        write list = smbtest
        guest ok = yes
        browsable =yes
        writable = yes
#       create mask = 0755
#       directory mask = 0755
        #valid users = @devteam
```

重启smb服务
```sh
systemctl restart smb.service
```

- MacOS 访问共享文件
  - 点击 Finder 前往菜单中的「前往服务器」
  - 在连接服务器对话框中输入「smb://192.168.1.6」
  - 选择要访问的文件夹



#### nfs

centos服务端

```sh
# ----- 安装/ 启动
yum -y install nfs-utils rpcbind

systemctl start nfs-server
systemctl start rpcbind

systemctl enable nfs-server
systemctl enable rpcbind
```

`vi /etc/exports` 添加以下内容
```
/opt/nfsShare *(rw,sync,insecure)
```

mac客户端
```sh
# 挂载
sudo mount -t nfs jasonzeng.top:/opt/nfsShare/ /private/nfs

# 推出
sudo umount /private/nfs
# or
sudo umount jasonzeng.top:/opt/nfsShare
```


#### 挂载硬盘 ？？
[centos7下挂载U盘和移动硬盘](https://blog.csdn.net/u010048823/article/details/51306175)
[Centos磁盘分区和挂载](https://www.jianshu.com/p/dbb2e5c787a0)
[centos7重新调整分区大小](https://blog.csdn.net/perfectzq/article/details/73606119)

[Linux 桌面玩家指南：11. 在同一个硬盘上安装多个 Linux 发行版](https://www.cnblogs.com/youxia/p/LinuxDesktop011.html)


- 挂载u盘
```sh
mount /dev/sdb1 /mnt/usb/
umount /mnt/usb/
```

## 存在的问题
- jellyfin 无法识别视频文件， 识别的电影局域网内也无法播放





## 其他

- 启动简单服务器测试防火墙情况
```
python -m SimpleHTTPServer 8001
```

- 监控cpu温度脚本
crontab -e
```
*/5 * * * * /root/scripts/CPU-temperature-script.sh 
```

```bash
#!/bin/bash

TEMPERATURE=$(sensors | grep "Core 0" | cut -d + -f 2 | cut -d . -f1)

if [ $TEMPERATURE -ge 45 ]; then
	echo "`date` $TEMPERATURE"  > /data/logs/cpu_temp.log
fi
```


## 泛域名解析

通过三级域名访问，nginx只监听80端口，内部端口通过域名映射
```sh
upstream jellyfin {
        server 127.0.0.1:8096;
}
upstream dockerui {
        server 127.0.0.1:8900;
}
# nas泛域名解析  nas-*.xxxxxxxx.top
server {
        listen 80;
        listen [::]:80;
        server_name  ~^nas-(?<subdomain>.+)\.xxxxxxxx\.top$;
        index  index.html index.htm;
        fastcgi_intercept_errors on;
        error_page  404      = /404.html;
        location / {
                proxy_pass http://$subdomain;
                proxy_redirect off;
       }
}
```
之后可以通过`nas-jellyfin.xxxx.top`访问jellyfin应用


## 参考
- [智能居家-用CentOS打造家庭NAS](https://zhuanlan.zhihu.com/p/168477471)





- 尝试未成功。。。

docker run -d \
    --name unlockmusic \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -p 16380:8080 \
p3terx/unblockneteasemusic \
    -o kuwo qq migu


docker run -d \
    --name unlockmusic \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -p 16380:8080 \
    -p 16381:8081 \
    -v /usr/local/share/ca-certificates/server.crt:/UnblockNeteaseMusic/server.crt \
    -v /usr/local/share/ca-certificates/server.key:/UnblockNeteaseMusic/server.key \
p3terx/unblockneteasemusic \
    -p 8080:8081 \
    -o kuwo qq migu \
    -s -e "https://music.163.com"

docker run -d \
  -p 80:8080 \
  nondanee/unblockneteasemusic