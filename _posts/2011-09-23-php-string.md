---
layout: post
title: "10+ 字符串相关的 PHP 代码片段"
description: "10+ 字符串相关的 PHP 代码片段"
category: PHP
tags: [PHP]
---
{% include JB/setup %}

<p>1、自动移除字符串中的 HTML 标记</p>

<p>在用户表单中，你可能希望移除所有不必要的 HTML 标记。使用 strip_tags() 函数可以简单地做到这一点：</p>

    $text = strip_tags($input, "");


<p>2、获取 $start 和 $end 之间的文本</p>

<p>这是一种每个网站开发人员应该收纳在开发工具箱的功能：给定一个字符串，一个起始位置，一个结束为止，并返回包含在 $start 和 $end 两者之间的文本。</p>

    function GetBetween($content,$start,$end){
        $r = explode($start, $content);
        if (isset($r[1])){
            $r = explode($end, $r[1]);
            return $r[0];
        }
        return '';
    }


<p>3、将 URL 转换为超链接</p>

<p>如果你在 WordPress 博客的评论表单中添加了 URL，它会被自动转换为超级链接。如果你想要在网站上实现同样的功能，可以使用以下代码：</p>

    $url = "芒果 (http://www.mangguo.org)";
    $url = preg_replace("#http://([A-z0-9./-]+)#", '$0', $url);


<p>4、将文本分割为 140 字符的数组</p>

<p>大家都知道，Twitter 仅仅接受 140 字符以内的消息。如果你希望与这个流行的即时信息网站交互，肯定会喜欢这个功能，这将允许对留言截断为 140 个字符。</p>

    function split_to_chunks($to,$text){
        $total_length = (140 - strlen($to));
        $text_arr = explode(" ",$text);
        $i=0;
        $message[0]="";
        foreach ($text_arr as $word){
            if ( strlen($message[$i] . $word . ' ') <= $total_length ){
                if ($text_arr[count($text_arr)-1] == $word){
                    $message[$i] .= $word;
                } else {
                    $message[$i] .= $word . ' ';
                }
            } else {
                $i++;
                if ($text_arr[count($text_arr)-1] == $word){
                    $message[$i] = $word;
                } else {
                    $message[$i] = $word . ' ';
                }
            }
        }
        return $message;
    }


<p>5、从字符串中移除 URL</p>

<p>为了获得流量或者反向链接，很多访客会发布大量含有网址信息的博客评论，这个代码片段可以对其进行有效防范：</p>

    $string = preg_replace('/\b(https?|ftp|file):\/\/[-A-Z0-9+&@#\/%?=~_|$!:,.;]*[A-Z0-9+&@#\/%=~_|$]/i', '', $string);


<p>6、转换字符串为缩略标题</p>

<p>创建缩略标题（通常称之为 permalink，即固定链接）有利于 SEO，以下函数以一个字符串作为参数，并返回良好的缩略字符串。简洁有效，值得尝试！</p>

    function slug($str){
        $str = strtolower(trim($str));
        $str = preg_replace('/[^a-z0-9-]/', '-', $str);
        $str = preg_replace('/-+/', "-", $str);
        return $str;
    }


<p>7、解析 CSV 文件</p>

<p>CSV（逗号分隔的值文件）是存储数据的简单方式，使用 PHP 解析也很容易。不信你可以动手试试以下代码片段。</p>

    $fh = fopen("contacts.csv", "r");
    while($line = fgetcsv($fh, 1000, ",")) {
        echo "Contact: {$line[1]}";
    }


<p>8、检索字符串中的另一个字符串</p>

<p>如果某个字符串包含在另一个字符串中，并且必须检索出来，这里有一个绝妙的方法：</p>

    function contains($str, $content, $ignorecase=true){
        if ($ignorecase){
            $str = strtolower($str);
            $content = strtolower($content);
        }
        return strpos($content,$str) ? true : false;
    }


<p>9、检测某个字符串是否以指定的模式开始</p>

<p>有些语言比如 Java 具有一个 startWith 方法，允许你检测某个字符串是否以指定的模式开始。不幸的是，PHP 不具备这种内建函数。但我们可以自己动手丰衣足食，实现也很简单：</p>

    function String_Begins_With($needle, $haystack {
        return (substr($haystack, 0, strlen($needle))==$needle);
    }


<p>10、从字符串中提取电子邮件地址</p>

<p>有没有想过那些发垃圾邮件的人是如何得到邮件地址的？这很简单，他们只需对网页进行简单的 HTML 解析即可提取电子邮件。此代码需要一个字符串作为参数，并打印所包含的电子邮件地址。芒果告诫：请勿使用此代码制造垃圾邮件！</p>

    function extract_emails($str){
        // This regular expression extracts all emails from a string:
        $regexp = '/([a-z0-9_\.\-])+\@(([a-z0-9\-])+\.)+([a-z0-9]{2,4})+/i';
        preg_match_all($regexp, $str, $m);

        return isset($m[0]) ? $m[0] : array();
    }

    $test_string = 'This is a test string...

            test1@example.org

            Test different formats:
            test2@example.org;
            <a href="test3@example.org">foobar</a>
            <test4@example.org>

            strange formats:
            test5@example.org
            test6[at]example.org
            test7@example.net.org.com
            test8@ example.org
            test9@!foo!.org

            foobar
    ';

    print_r(extract_emails($test_string));
