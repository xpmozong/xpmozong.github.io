---
layout: post
title: "PHP之call_user_func_array用法"
description: "PHP之call_user_func_array用法"
category: PHP
tags: [PHP]
---
{% include JB/setup %}

<ul>
    <li>作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a></li>
    <li>本文地址：http://xpmozong.github.io/php/2012/01/09/php-call_user_func_array/</li>
    <li>转载请注明出处</li>
</ul>

<p>直接上最简单的</p>

    header('Content-Type:text/html;charset=utf-8');
    class call_func{
        function func0() {
            echo 'func0';
        }

        function func1($a) {
            echo $a;
        }

        function func2($a, $b) {
            echo $a+$b;
        }

        function func3($a, $b, $c) {
            echo $a+$b+$c;
        }
    }

    function otest() {
        $args = func_get_args();    
        $num = func_num_args();
        call_user_func_array(array('call_func','func'.$num), $args);
    }

    otest(1,1,1);


<p>mixed call_user_func_array ( callback function, array param_arr ) 和 mixed call_user_func ( callback $function [, mixed $parameter [, mixed $... ]] ) 相似</p>

<p>还可以这样实例化类的，厉害吧！！！</p>
