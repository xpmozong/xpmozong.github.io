---
layout: post
title: "saltstack 学习小记"
description: "saltstack 学习小记"
category: python
tags: [python,saltstack]
---
{% include JB/setup %}

<ul>
    <li>作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a></li>
    <li>本文地址：http://xpmozong.github.io/python/2013/09/05/python-saltstack/</li>
    <li>转载请注明出处</li>
</ul>

<p>saltstack的基本介绍</p>
<p>salt是一个新的基础平台管理工具。很短的时间即可运行并使用起来, 扩展性足以支撑管理上万台服务器，数秒钟即可完成数据传递. 经常被描述为 Func加强版+Puppet精简版。</p>

<p>salt的安装</p>

<p>首先添加RPM源:</p>
    rpm -ivh http://mirrors.sohu.com/fedora-epel/6/x86_64/epel-release-6-8.noarch.rpm

<p>1、安装服务端</p>
    yum install salt-master
<p>可以修改配置文件，vim /etc/salt/master 将auto_accept 改为 True，这样可以自动接收客户端</p>

    file_roots:
        base:
            - /srv/salt

<p>这个是可以控制上传的路径</p>
<p>启动服务端： /etc/init.d/salt-minion start</p>

<p>2、安装客户端</p>
    yum install salt-minion
<p>修改配置文件，vim /etc/salt/minion master:xxxxxxx   id:xxxxxx</p>
<p>启动客户端：/etc/init.d/salt-minion start</p>

<p>3、salt命令</p>
<p>salt-key -L  列表</p>
<p>salt '*' grains.ls  查看grains分类</p>
<p>salt '*' grains.items 查看grains所有信息</p>
<p>salt '*' grains.item osrelease 查看grains某个信息</p>
<p>命令啥的，就不解释了，参考<a target="_blank" href="http://wiki.saltstack.cn/modules/all">salt内置执行模块列表</a></p>

<p>4、相关文档 </p>
<p><a target="_blank" href="http://wiki.saltstack.cn/">saltstack社区</a></p>

<p>5、salt开发</p>
<p>python开发</p>

    import salt.client
    client = salt.client.LocalClient()
    ret = client.cmd('*', 'cmd.run', ['ls -l'])
    print ret


<p>6、<a href="http://www.ituring.com.cn/article/41632">SaltStack中的文件服务器</a></p>


<p>最近在开发一个salt管理系统，敬请期待！</p>