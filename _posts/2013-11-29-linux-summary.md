---
layout: post
title: "linux 小总结"
description: "linux 小总结"
category: "LINUX"
tags: [LINUX]
---
{% include JB/setup %}

<p>1、初始安装</p>
    yum install make apr* autoconf automake gcc gcc-c++ openssl openssl-devel pcre-devel gd  kernel keyutils perl kernel-headers compat* mpfr cpp glibc libgomp libstdc++-devel ppl cloog-ppl keyutils-libs-devel libcom_err-devel libsepol-devel libselinux-devel krb5-devel zlib-devel libXpm* freetype libjpeg* libpng* ncurses* libtool* libxml2 libxml2-devel patch
    
<p>2、日志20行</p>
    cat error.log | awk '{print $1}'|sort|uniq -c|sort -nr|head -20

<p>3、运行C程序</p>
    yum -y install gcc
    yum -y install gcc-c++ 
    yum install make
    // 编译 
    gcc hello.c -o hello

<p>4、加全局变量</p>
    编辑： vim /root/.bash_profile
    添加： export PATH=$PATH:/usr/local/go/bin
    运行： source /root/.bash_profile
    可以通过 echo $PATH 来查看环境变量
    vim .bashrc
    source .bashrc

<p>5、Centos查看端口占用情况命令，比如查看80端口占用情况使用如下命令：</p>
    lsof -i tcp:80

<p>6、列出所有端口</p>
    netstat -ntlp

<p>7、查看进程</p>
    ps -ef | grep nginx

<p>8、按照这个步骤启动uwsgi</p>
    ps aux | grep uwsgi
    pkill uwsg
    screen
    uwsgi -x /data/funny/uwsgi.xml
    chown -R www:www /tmp/funny.sock

<p>9、列出文件夹下文件夹大小</p>
    du -sh /data/*  

<p>10、定时执行修改</p>
    crontab -e
    /etc/init.d/crond restart

<p>11、复制</p>
    // 复制指定目录的文件到当前目录，并重命名
    cp ~/.bashrc bashrc_bak

    // 强制复制指定目录的文件到当前目录，而不管当前目录是否含有该文件
    cp -f ~/.bashrc bashrc

    // 复制指定目录到当前目录
    cp -r /root/test .
    cp -r /root/test/ .

    // 复制指定目录的文件到指定目录
    cp ~/.bashrc /bak/.bashrc
    // 若提示：
    cp: omitting directory
    // 则采用：
    cp -r ~/.bashrc /bak/.bashrc
    // 若提示：
    cp:overwrite
    // 则采用加斜杠：
    /cp -r -f ~/.bashrc /bak/.bashrc

    // 在复制时将源文件的全部属性也复制过来。若不指定参数，则目标文件与源文件属性可能不一致。
    cp -a ~/.bashrc /bak/.bashrc

    // 若两个文件夹要保证同步，一个文件的改了，另一个文件也跟着改，但是要保证两个文件的文件都是最新的。
    cp -u /src/.bashrc /bak_src/bashrc

<p>12、重命名文件夹</p>
    mv oldfilename newfilename

<p>13、防火墙</p>
    // 关闭
    /etc/init.d/iptables stop
    // 添加例外端口 
    vim /etc/sysconfig/iptables
    -A INPUT -p tcp -m tcp --dport 55 -j ACCEPT

<p>14、查找文件</p>
    find / -name access_log

<p>15、网桥</p>
    brctl show

<p>16、noVNC</p>
    yum install python-devel
    nohup /data/noVNC/utils/launch.sh &
    // 普通访问
    /data/noVNC/utils/websockify.py -D 8000 192.168.1.1:5900
    http://192.168.1.1:6080/vnc_auto.html?host=192.168.1.1&port=8000
    // proxy访问
    /data/noVNC/utils/websockify --target-config=/data/noVNC/vnc_tokens 29876
    http://192.168.1.1:6080/vnc_auto.html?host=192.168.1.1&port=29876&token=212oukhdvszujihzqvm

<p>17、virsh、qemu命令</p>
    // 远程连接
    virsh -c qemu+tls://192.168.1.1:23/system list --all
    // 创建磁盘
    qemu-img create -f qcow2 -o preallocation=metadata /data/vm/test2.qcow2 8G
    // 编辑虚拟机文件
    virt-edit -d 912-2 /root/meta.js
    // 挂载虚拟机磁盘
    guestmount --rw -a /data/qyinstances/instance-11354/disk.qcow2 -m /dev/sda1 /mnt/qyvms/
    
    fusermount -u /mnt/tmp1

<p>18、将文件转成镜像</p>
    mkisofs -r -o meta.iso meta.js

<p>19、挂载文件</p>
    mount /dev/sr0 /media/

<p>20、查看硬盘还剩多少容量</p>
    df -h
    fdisk -l

<p>21、获取IP地址</p>
    dhclient -d eth0

<p>22、随机生成mac地址</p>
    MACADDR="52:54:$(dd if=/dev/urandom count=1 2>/dev/null | md5sum | sed 's/^\(..\)\(..\)\(..\)\(..\).*$/\1:\2:\3:\4/')"; echo $MACADDR

<p>23、更改文件或文件夹所属</p>
    chown qemu.qemu /dev/kvm

<p>24、修改ip</p>
    vim /etc/sysconfig/network-scripts/ifcfg-em1

<p>25、查看网线有没有连接</p>
    mii-tool em2

<p>26、获得掩码</p>
    cat /etc/sysconfig/network-scripts/ifcfg-public | grep -i netmask  

<p>27、获得网关</p>
    route -n | awk '{print $2}' | tail -1

<p>28、salt学习</p>
    salt-key -L
    salt-key -d opstest
    salt "*" grains.items
    salt "*" grains.item id
    salt "*" grains.item ipv4
    // 分发文件 
    salt 'hadoop1' cp.get_file salt://test.txt /data/test.txt

<p>29、SCP</p>
    // 从本地复制到远程
    scp -P 22 /data/iso/test.txt admin@192.168.1.1:/data/iso/
    // 从远程复制到本地
    scp -P 22 root@192.168.1.1:/data/vm/test.txt /data/iso/test.txt

<p>30、判断一文件是否存在</p>
    test -f /data/iso/centos6.3.qcow2;echo $?
    // 0为有文件，其他为无文件

<p>31、ssh 登录</p>
    ssh -p 22 -i id_rsa admin@192.168.1.1

<p>32、rpm删不掉的话 用这个</p> 
    rpm -e opennebula-sunstone-4.0.0-1.x86_64 --noscripts

<p>33、添加用户并登陆</p>
    useradd opsvmadmin
    groupadd opsvmadmin
    usermod -a -G opsvmadmin opsvmadmin
    vim /etc/ssh/sshd_config
    // 加入 AllowGroups opsvmadmin
    /etc/init.d/sshd restart

<p>34、检测某段内ip是否可用</p>
    fping -t 500 -g  192.168.1.1 192.168.1.128

<p>35、ll 不可用的时候</p>
    vim ~/.bashrc  alias ll='ls -l --color=auto'

<p>36、看某个文件逐渐增加的</p>
    watch "ls -l"

<p>37、打包</p>
    tar zcvf noVNC.tar.gz noVNC/

<p>38、证书使用包</p>
    yum install -y gnutls-utils

<p>39、查看iptables 列表</p>
    iptables -L -n

<p>40、输出CPU使用率、输出内存使用率</p>
    cat /proc/stat | grep cpu | head -1 | awk '{sum=$2+$3+$4+$5+$6+$7+$8;use=$2+$3+$4+$6+$7+$8;printf "%s\t%.2f%%\n",$1,(use*100/sum)}'
    free | grep Mem: | awk '{use=$3-$6-$7;printf "%s\t%.2f%%\n",$1,(use*100/$2)}'
