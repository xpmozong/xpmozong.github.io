---
layout: post
title: "smarty 生成静态化html文件"
description: "smarty 生成静态化html文件"
category: PHP
tags: [PHP,smarty]
---
{% include JB/setup %}

*	作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a>
*	本文地址：http://xpmozong.github.io/php/2011/11/25/php-smarty/
*	转载请注明出处

静态化网页，是个基本技能。下面是smarty静态化。

打开Smarty.class.php 文件

加入以下代码

    // 循环创建目录 
    function mk_dir($dir, $mode = 0777) 
    { 
        if($dir == '') return true;
        if (is_dir($dir) || @mkdir($dir,$mode)) return true; 
        if (!$this->mk_dir(dirname($dir),$mode)) return false; 
        return @mkdir($dir,$mode); 
    } 

    /**
     * 页面静态化
     * 
     * @param string $file_name  
     * @param string $content
     *
     */
    function makeHtmlFile($filedir, $file_name, $content)
    {
        $filedir = $filedir == '' ? dirname(__FILE__).'/../../' : $filedir;
        //目录不存在就创建
        if (!file_exists($filedir.$file_name)){
            $this->mk_dir($filedir);
        }
        if(!$fp = fopen($filedir.$file_name, "w+", true)){
            echo '文件打开失败！';
            return false;
        }
        if(!fwrite($fp, $content)){
            echo '文件写入失败！';
            return false;
        }
        fclose($fp);
        if(chmod($filedir.$file_name,0777)){
            return true;
        }else{
            echo '改变文件权限失败！';
            return false;
        }   
    }


加好了之后，就是调用了。怎么调用呢，看一下代码：

    //  $smarty->display('index.html');
    $content = $smarty->fetch('index.html');
    $smarty->makeHtmlFile('html/index.html',$content);//生成


然后到html目录下，查看，就可以看到生成的静态化文件。