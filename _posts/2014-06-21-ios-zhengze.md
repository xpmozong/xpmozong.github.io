---
layout: post
title: "IOS 正则表达式"
description: "IOS 正则表达式"
category: "IOS"
tags: [IOS]
---
{% include JB/setup %}

<ul>
    <li>作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a></li>
    <li>本文地址：http://xpmozong.github.io/ios/2014/06/21/ios-zhengze/</li>
    <li>转载请注明出处</li>
</ul>

熟悉php的都知道preg_match_all是一个超级强大的正则表达式函数。在数据采集的世界里，有了她，几乎是所向披靡，神挡杀神，佛挡杀佛。

由于Object-C有没有类似preg_match_all的函数。 NSPredicate 不是很理想。而RegexKit很强大。

下载地址：<a href="https://github.com/wezm/RegexKitLite">RegexKit</a>

需要添加一个依懒库：libicucore.dylib

具体用法，首先引入头文件：

    #import "RegexKitLite.h"

    ASIHTTPRequest *htmlRequest = [ASIHTTPRequest requestWithURL:[NSURL URLWithString:str]];
    [htmlRequest setRequestMethod:@"GET"];
    [htmlRequest setTimeOutSeconds:30];
    [htmlRequest setCompletionBlock:^{
        NSString *resultStr = [[[NSString alloc] initWithData:htmlRequest.responseData encoding:NSUTF8StringEncoding] autorelease];
        NSString *parten = @"<a class=\"v-link\" href=\"(.*)\" target=\"video\" title=\"(.*)\"></a>";
        NSArray *resultArray = [resultStr arrayOfCaptureComponentsMatchedByRegex:parten];
        NSLog(@"%@", resultArray);
    }];
    [htmlRequest startSynchronous];


可根据自己的需要 重写 

    NSString *parten = @"<a class=\"v-link\" href=\"(.*)\" target=\"video\" title=\"(.*)\"></a>";

好了，就说到这里。
