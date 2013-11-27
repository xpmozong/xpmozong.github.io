---
layout: post
title: "redis 浅见"
description: "redis 浅见"
category: "redis"
tags: [redis]
---
{% include JB/setup %}

以前只会安装redis，和使用string类型，这个星期，为了使自己更懂一点，所以看了很多关于redis的文章。所以写了这篇浅谈博客，有观点不准确的地方，可以纠正我。
在NoSql领域中，redis占据着重要位置。她小儿精悍，被比喻作AK-47。
下面要讨论的是redis的数据类型，关于aof日志文件，以及rediska的使用。

一、Redis的数据类型Strings, integers, lists, hashes, sets, sorted sets
他们的用法如下：

    1、Strings

    > set foo bar
    OK
    >get foo
    "bar"

    2、integer

    >set mykey "10"
    OK 
    >incr mykey
    (integer) 11
    >get mykey
    "11"
    >decr mykey
    (integer) 10
    >get mykey
    "10"


    3、Lists

    >rpush foo baz
    (integer) 1
    >rpush foo qux
    (integer) 2
    >lpush foo bar
    (integer) 3
    >lrange foo 0 -1
    "bar"
    "baz"
    "qux"
    >lrange foo 0 1
    "bar"
    "baz"
    >lpop foo
    "bar"
    >rpop foo
    "qux"

    4、Hashes

    >hset foo bar baz
    (integer) 1
    >hset foo qux quxx
    (integer) 1
    >hget foo bar
    "baz"
    >hgetall foo
    "bar"
    "baz"
    "qux"
    "quxx"

    5、Set

    >sadd m1 jan
    (integer) 1
    >sadd m1 feb
    (integer) 1
    >sismember m1 jan
    (integer) 1
    >sismember m1 feb
    (integer) 1
    >smembers m1
    "feb"
    "jan"
    >sadd m2 feb
    (integer) 1
    >sismember m2 jan
    (integer) 1
    >sismember m2 mar
    (integer) 1
    >sinter m1 m2
    "feb"
    >sdiff m1 m2
    "jan"
    >sunion m1 m2
    "mar"
    "jan"
    "feb"

    6、sorted sets

    >zadd z1 1 jan
    (integer) 1
    >zadd z1 2 feb
    (integer) 1
    >zadd z1 3 mar
    (integer) 1
    >zscore z1 feb
    "2"
    >zrange z1 0 1 withscores
    "jan"
    "1"
    "feb"
    "2"
    "mar"
    "3"

<p>由上可知，我觉得理想的存储数据类型是lists和hashes。</p>
<p>昨天晚上翻了一遍算法导论的数据结构那一张。Lists相当于队列，可以lpush，rpush，lpop，rpop。Hashes相当于指针指向一块，这一块里可以包含很多数据。</p>

<p>二、Redis进阶教程-aof(append only file)日志文件</p>
<p>原文出处:http://lgone.com/html/y2010/757.html</p>
<p>今天看了Redis源码两个多小时,搞清楚了append only file(AOF)日志的rotate原理.官方文档里面对这部分做了单独的说明,但是就我看来,作者不够优美的用词,完全会让人理解错误.(PS:我要不是觉得文档有问题也不至于看源码)</p>
<p>何为AOF?</p>
<p>在Redis配置文件中有一个叫appendonly的选项,可以写yes或no.这个选项就是负责是否开启AOF日志的开关.AOF日志,你可以简单理解为MySQL binlog一样的东西,作用就是记录每次的写操作,在遇到断电等问题时可以用它来恢复数据库状态.但是他不是bin的,而是text的.一行一行,写得很规范.如果你是一台redis,那你也能人肉通过它恢复数据.</p>
<p>扩展知识:</p>
<p>Redis有三种类型的落地文件:</p>
<p>数据文件-在配置中可设置其位置及文件名,默认文件名dump.rdb</p>
<p>日志文件-在配置中也可以配置.当然,在你是以daemon方式运行的时候,这个值就不要设置为stdout了,这么设置会自动被换成/dev/null</p>
<p>AOF文件-也就是我们这篇文章的主角,他的作用是用于数据恢复.他除了设置是否开启外,还可以设置开启后以何种方式写日志.这个何种一共是三种,1是每次写操作都保证将fsync()来完成的,2是每秒调用一次fsync(),3是从不调用,让操作系统自己来同步.当然,设置为不同,效率会不同,你的数据损失风险也不同.</p>
<p>运行流程</p>
<p>既然是log文件,而且是要用于恢复的,那么我们动动脚趾都能想到,这玩意肯定会越来越大,不管你的应用是大是小,如果AOF文件只增不减的话,那文件将会无限长大,这个问题是所有binlog都会遇到的.而通常遇到这种问题都会有一个rotate方案.就是当日志达到一定大小或者每隔一段时间将日志写到新的一个文件中,旧日志文件可以用来备份或其它.</p>
<p>而Redis的AOF还和rotate略有不同,他用了一种比较简单的方法,就是先给当前的所有数据做一个快照.然后再在这个快照的基础上写接下来的日志.</p>
<p>照快照的好处是,你之前可能用了10w次操作共改变了100条数据(比如在一条数据上进行了多次操作).那这时你AOF中的10w条写操作记录就变成了100条记录.相当于将前面的执行全部合并了.原本很大AOF变小了.</p>
<p>执行这个操作的命令是:BGREWRITEAOF (background rewrite append only file)</p>
<p>这个快照长什么样呢?基本和一般的写日志没什么两样,也是一条一条的写记录..比如现在Redis中总共存了3条string类型的数据a=>1,b=>2,c=>3.那这个快照的基本内容就是写入a=>1,写入b=>2,写入c=>3.</p>
<p>这时候要用AOF进行恢复的时候,只要先执行了前面几条,就能够恢复当前状态,然后再执行之后来的写操作,就能完全重现数据了.</p>
<p>内部实现</p>
<p>我们大概知道了执行BGREWRITEAOF时都发生了什么,下面来说一下Redis是如何实现的.分下面几步:</p>
<p>fork! Redis通过fork产生子进程.</p>
<p>子进程对当前数据执行遍历操作,将当前所有数据都生成一条写入日志,将这些日志写入一个临时文件.(其实是子进程写了一个临时文件,又再rename成了另一个临时文件)</p>
<p>父子进程是并行执行的,在子进程遍历并写临时文件的时候,父进程在照常接收请求,处理请求,写AOF,不过这时他是把新来的AOF写在一个缓冲区中.</p>
<p>当子进程完成遍历操作,写完临时文件后,就会退出.这时父进程的wait3函数会接收到子进程退出的消息,他会把自己现在收集在缓冲区中的所有AOF追加在临时文件中.</p>
<p>最后一步,把临时文件rename一下,改名为appendonly.aof,这时原来的aof文件被覆盖.整个过程完成.</p>
<p>如果你的AOF文件稍微大点,你可以在一个终端执行BGREWRITEAOF,然后立刻ls 连着查看几次redis的data目录,就可以看到,先生成了一个临时文件,临时文件比原来的appendonly.aof小一些,然后临时文件消失,而原来的appendonly.aof变小了,其实就是临时文件rename成了appendonly.aof..覆盖了原来的大文件.看起来像是临时文件消失了.</p>
<p>其它</p>
<p>可能你要说,随着数据的增多,aof文件肯定也是越来越大的啊..这个没错,因为当你有10w条记录的时候,你至少要有10w条纯添加日志.然而这时候你的数据文件也应该够大了吧..更何况这日志文件呢.</p>

<p>三、在windows环境下，phper想使用php操作redis，那怎么办呢？</p>
<p>给大家推荐一个rediska，使用rediska来代替phpredis模块。</p>
<p>用法很简单，如下：</p>

    ---
    require 'Rediska.php';
    $rediska = new Rediska();
    $rediska ->connect('127.0.0.1',6379);
    $rediska ->set('test','hello world!');
    echo $rediska ->get('test');
    ---