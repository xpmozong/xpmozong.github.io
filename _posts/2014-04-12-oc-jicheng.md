---
layout: post
title: "OC 继承"
description: "OC 继承"
category: OC
tags: [iOS,OC]
---
{% include JB/setup %}

*	作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a>
*	本文地址：http://xpmozong.github.io/ios,oc/2014/04/12/oc-jicheng/
*	转载请注明出处

OC中的继承是单向的，继承具有传递性，子类继承父类的特征和行为，子类扩展父类，更具体。

OC中的继承,即⼀个类继承另一个类。

被继承的类称为⽗父类或超类。

继承的类称为⼦子类。

继承的实现如下：
    
    #import "Person.h"
    @interface Student : Person
    @end

冒号前为子类名称，冒号后为父类名称

继承的特点

OC中只允许单继承。

没有父类的类称为根类,OC中的根类是NSObject(祖宗)。 继承的内容:除了私有变量之外的所有实例变量和方法。

子类可以重写父类的方法。