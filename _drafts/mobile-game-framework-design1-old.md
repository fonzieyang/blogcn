---

layout:     post
title:      "手游框架设计&lt;一&gt;"
subtitle:   "过去两年的手游框架设计总结"
categories: 游戏开发
tags: [框架, tag]
header-img: "img/lf2.jpg"

---

## 游戏界面开发

### 1) 快速获取控件

这里可以把UI的制作通过编辑器交给美术完成界面的拼接，而后你直接用UI进行开发，到这里很多项目拿到UI都是写代码加载控件，比如：


~~~lua
local label1 = GetUI(ui, "label1")
local label2 = GetUI(ui, "label2")
local btn1   = GetUI(ui, "btn1")
...

label1:setString("hi")
~~~

这样写一大堆，但其实我们可以抽成一句就完成了，你从此只管直接用，比如：

~~~lua

UIManager.configUI(ui)
ui.label1:setString("hi")

~~~

其中UIManager:configUI的用途就是将控件中的所有名字查询出来配置好，原理很简单，能让我们非常快速地完成UI工作。

### 2) 约定优于配置

在一个管理良好的游戏，不可能让你把资源写死在代码里面的，但是我们配置资源路径然后读取又很繁琐痛苦，这明明可以自动完成的事情，为什么这么麻烦呢？假如我们都通过约定，那么是不是就省去了大量的配置呢？

在过去，我们这样加载资源：

~~~lua
-- 在某个地方统一配置
resId = "path/res.png"

-- 在另一个地方使用
local res = loadRes(resId)
....
~~~

现在：

~~~lua
-- 在管理器中做好约定
function ResMgr:createRes(str)
	return loadRes("path/" .. str .. ".png")
end

-- 还是直接用就可以，而且不用配置
ResMgr:createRes("hero01")
~~~

看出区别了吗？前一种方式需要配置resId，类似Android的方式，需要自己生成代码或者手写一次路径配置，虽然能用，但很僵硬，不好改动。后一种会让代码知道最少的东西，不用去管路径，只要知道资源的名字，通过约定就给你找到资源。因为路径没有在逻辑中扩散，我们可以基于此，可以做很多的重定向工作，比如加密解密，比如拼图等等。为什么？因为这样足够抽象，符合语义，而且不需要重复自己。