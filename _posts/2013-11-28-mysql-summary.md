---
layout: post
title: "mysql 小总结"
description: "mysql 小总结"
category: "mysql"
tags: [mysql]
---
{% include JB/setup %}

<ul>
    <li>作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a></li>
    <li>本文地址：http://xpmozong.github.io/mysql/2013/11/28/mysql-summary/</li>
    <li>转载请注明出处</li>
</ul>

<p>1、两个关联表的字段的数据需相同，这么同步</p>
    UPDATE tb1 f left join tb2 m on f.uid=m.uid set f.images_ischeck = m.images_ischeck where f.images_ischeck != m.images_ischeck
    UPDATE server_message m left join server_message_schedule s on m.server_id=s.server_id set s.server_cost = m.server_cost

<p>2、给两个关联表的相关字段加索引</p>
    CREATE INDEX idx_origin_id_id ON en_sentence_translated(origin_id,id)

<p>3、两个关联表，其中一个表示一对多的关系，用max、group by</p>
    SELECT cn.content cn_content,en.content en_content,MAX(en.id) FROM `cn_sentence_origin` cn
    LEFT JOIN en_sentence_translated en ON cn.id=en.origin_id 
    WHERE cn.file_id=628 GROUP BY cn.id

<p>4、给数据库加权限</p> 
    GRANT ALL ON weblog.* TO wqylog@127.0.0.1 IDENTIFIED BY "xuping890327";
<p>用户名：wqylog  密码：xuping890327</p>

<p>5、mysql 清空表</p>
    truncate table 表名;

<p>6、utf-8字符排序</p>
    SELECT * FROM `origin_file` WHERE parentid=0 ORDER BY CONVERT( name USING gbk ) COLLATE gbk_chinese_ci ASC

<p>7、根据IP排序</p>
    SELECT FROM server_message ORDER BY INET_ATON(server_message.server_ip1)

<p>8、根据ids 排序</p>
    select * from server_message m  where m.server_id in(4,5,2,8) order by field(m.server_id,'4,5,2,8')