---
layout: post
title: "PHP扩展开发 - 第一个扩展"
description: "PHP扩展开发 - 第一个扩展"
category: "PHP"
tags: [PHP]
---
{% include JB/setup %}

学习PHP扩展开发，是PHPer的必学课程。接着上一节课程，<a href="/php/2013/12/05/lnmp/" target="_blank">Linux + Nginx + Mysql + PHP 编译安装</a>。

需求：比如开发一个叫做 hello_world 的扩展，扩展里就一个函数 hello_world_test()，输入一个字符串，函数返回：Your input string: xxxxx! 。

环境： php-5.3.16

<p><b>第一步、生成代码</b></p>

PHP为了扩展开发的方便，提供了一个类似代码生成器的工具ext_skel

    cd /opt/php-5.3.16/ext/
    ./ext_skel --extname=hello_world --proto=hello_world.skel
    cd hello_world/

生成代码啦

<p><b>第二步，修改配置</b></p>

然后修改config.m4文件将10、11、12三行最前面的dnl删除掉，就是将

    dnl PHP_ARG_WITH(ccvita, for ccvita support,
    dnl Make sure that the comment is aligned:
    dnl [  --with-ccvita             Include ccvita support])

修改为

    PHP_ARG_WITH(ccvita, for ccvita support,
    Make sure that the comment is aligned:
    [  --with-ccvita             Include ccvita support])

<p><b>第三步，实现功能</b></p>

修改源码php_hello_world.h文件

添加

    PHP_FUNCTION(hello_world_test);

然后修改源码 hello_world.c 文件

添加

    PHP_FUNCTION(hello_world_test)
    {
            char *arg = NULL;
            int arg_len, len;
            char *strg;

            if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "s", &arg, &arg_len) == FAILURE) {
                    return;
            }

            len = spprintf(&strg, 0, "Hello World! Your input string: %s\n", arg);
            RETURN_STRINGL(strg, len, 0);
    }

<p><b>第四步，编译扩展</b></p>

保存后，开始编译

    /usr/local/php/bin/phpize
    ./configure --with-php-config=/usr/local/php/bin/php-config
    make
    make test
    make install

<p><b>第五步，添加扩展</b></p>

现在看看是不是有个 /usr/local/php/lib/php/extensions/no-debug-non-zts-20090626/hello_world.so

编辑php.ini配置文件，把扩展加入进去：

添加模块：

    extension = hello_world.so

保存退出。

<p><b>第六步，检查安装结果</b></p>

编辑test.php文件

    echo "<pre>";
    $str = confirm_hello_world_compiled(hello_world);
    echo $str."<br>";
    
    print_r(get_loaded_extensions());
    print_r(get_extension_funcs('hello_world'));
    echo hello_world_test('My first php extension! ');


看看有没有结果输出！