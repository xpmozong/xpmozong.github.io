---
layout: post
title: "iOS target/action设计模式"
description: "iOS target/action设计模式"
category: "iOS"
tags: [iOS]
---
{% include JB/setup %}

<ul>
    <li>作者：<a href="http://weibo.com/xpmozong" target="blank">寞踪</a></li>
    <li>本文地址：http://xpmozong.github.io/ios/2014/04/16/ios-target-action/</li>
    <li>转载请注明出处</li>
</ul>


iOS中，有一种target/action设计模式，可以使代码高聚合，低耦合。

创建一个ActionView类

ActionView.h的文件内容为

    @interface ActionView : UIView
    @property (nonatomic, assign) id  target;
    @property (nonatomic, assign) SEL action;
    - (id)initWithFrame:(CGRect)frame target:(id)target action:(SEL)action;
    @end

ActionView.m的文件内容为

    - (id)initWithFrame:(CGRect)frame target:(id)target action:(SEL)action
    {
        self = [super initWithFrame:frame];
        if (self) {
            self.target = target;
            self.action = action;
        }
        return self;
    }
    - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
    {
        [_target performSelector:_action withObject:self];
    }


控制层引入ActionView.h文件
    
    ActionView *actionView = [[ActionView alloc] initWithFrame:CGRectMake(100, 100, 100, 100) target:self action:@selector(changeColor:)];
    actionView.backgroundColor = [UIColor blueColor];
    [self.view addSubview:actionView];
    [actionView release];

实现自定义的changeColor方法。


此过程就是target/action设计模式的实现过程。

<b>target/action和delegate对比

target…action

作用：降低耦合性（解耦）

机制：类内部target去执行action方法，参数self

特点：外界指定target和action

注意事项：如果action没实现，程序会crash

专注点：通常仅仅是为了触发target的action事件

delegate

作用：降低耦合性（解耦）

机制：delegate去执行协议中的方法，参数self

特点：外界仅能指定delegate，不能指定action，action在协议中已经声明

注意事项：如果request方法没实现，程序会crash，如果@optional方法没实现，程序不会crash。

专注点：一系列相关事件的处理（多个事件）


