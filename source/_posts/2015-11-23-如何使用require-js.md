---
title: 如何使用require.js?
tags: []
date: 2015-11-23 15:45:00
---

最近几天在学习一个javascript库require.js，也看了一些相关的教学视频，这里推荐一下幕课网阿当老师的《[阿当大话西游之Web组件](http://www.imooc.com/learn/99)》的教学视频，一整套看下来，参照视频里面的demo从头做一遍，对于require.js的使用以及web组件的编写挺有帮助的，作为菜鸟，看完后觉得获得更多的是一种编程思想的塑造！可以去看看！

言归正传，什么是require.js？

RequireJS是一个工具库，主要用于客户端的模块管理。它可以让客户端的代码分成一个个模块，实现异步或动态加载，从而提高代码的性能和可维护性。它的模块管理遵守AMD规范，模块与模块之间可以互相依赖，当然可能会有人会想，模块之间的依赖，要是没法正确地去按照特定顺序加载，会出现错误，AMD规范可以处理这种问题，AMD就是这样一种对模块的定义，使模块和它的依赖可以被异步的加载，但又按照正确的顺序。

AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

如何使用require.js?

将require.js下载下来，嵌入网页中，

<div class="cnblogs_code">
<pre>&lt;script data-main=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/main</span><span style="color: #800000;">"</span> src=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/require.js</span><span style="color: #800000;">"</span>&gt;&lt;/script&gt;</pre>
</div>

这里的data-main属性声明的是入口文件scripts/main.js，这里我们把.js后缀省略掉了。也有以下这种写法：

<div class="cnblogs_code">
<pre>&lt;script src=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/require.js</span><span style="color: #800000;">"</span> data-main=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/main</span><span style="color: #800000;">"</span> defer <span style="color: #0000ff;">async</span>=<span style="color: #800000;">"</span><span style="color: #800000;">true</span><span style="color: #800000;">"</span> &gt;&lt;/script&gt;</pre>
</div>

async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。同时，官方提供了 require.js和 jquery 的打包版本，于是也可以怎么引入：

<div class="cnblogs_code">
<pre>&lt;script src=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/require-jquery.js</span><span style="color: #800000;">"</span> data-main=<span style="color: #800000;">"</span><span style="color: #800000;">scripts/main</span><span style="color: #800000;">"</span> defer <span style="color: #0000ff;">async</span>=<span style="color: #800000;">"</span><span style="color: #800000;">true</span><span style="color: #800000;">"</span> &gt;&lt;/script&gt;</pre>
</div>

RequireJS通过define方法，将代码定义为模块；通过require方法，实现代码的模块加载。

使用define方法，可以将代码写在一个js文件，独立开来作为一个模块，如我建立一个animate模块(animate.js)，如下：

<div class="cnblogs_code">

define(function(){ 
	　　function animate(){ 
		　　　　this.name="animate";
	　　};
	　　**return** { 
			　　　　animate:animate ,
			　　　　dec:"这是一个描述"
	　　};
})

</div>

将你的模块代码放置在define(function(){ &nbsp;/*代码*/ &nbsp; });，然后将该模块return的对象暴露出来，可以供其他模块依赖此模块的时候，可以去调用这个模块的API。比如我们建立一个tabview模块(tabview.js)去依赖这个animate模块，

<div class="cnblogs_code">
<pre>define([<span style="color: #800000;">'</span><span style="color: #800000;">animate</span><span style="color: #800000;">'</span><span style="color: #000000;">],function(a){
    function tabview(){ 
        </span><span style="color: #0000ff;">this</span>.name= <span style="color: #800000;">'</span><span style="color: #800000;">tabview</span><span style="color: #800000;">'</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">this</span>.animate =<span style="color: #000000;"> a.animate.name;
        </span><span style="color: #0000ff;">this</span>.dec =<span style="color: #000000;"> a.dec;
    } 
    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> { tabview:tabview };
})</span></pre>
</div>

分析上面的代码，我们将animate模块引入，并给其赋予一个a的别名。那么在该模块不就可以调用animate模块里面的方法和属性了吗？

这里我们在多建一个treeview模块（treeview.js）,如下：

<div class="cnblogs_code">
<pre><span style="color: #000000;">define(function(){ 
    function treeview(){ 
        </span><span style="color: #0000ff;">this</span>.name=<span style="color: #800000;">"</span><span style="color: #800000;">treeview</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    };
    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> { treeview:treeview };
})</span></pre>
</div>

接下来，我们需要去使用前面我们定义好的模块，便可以require方法来实现，见其写在main.js中，如下：

<div class="cnblogs_code">
<pre>require([<span style="color: #800000;">'</span><span style="color: #800000;">tabview</span><span style="color: #800000;">'</span>,<span style="color: #800000;">'</span><span style="color: #800000;">treeview</span><span style="color: #800000;">'</span><span style="color: #000000;">],function(a,b){ 
    </span><span style="color: #0000ff;">var</span> tab = <span style="color: #0000ff;">new</span><span style="color: #000000;"> a.tabview();
    </span><span style="color: #0000ff;">var</span> tree = <span style="color: #0000ff;">new</span><span style="color: #000000;"> b.treeview();
    alert(tab.name);
    alert(tab.animate);
    alert(tab.dec);
    alert(tree.name);
});</span></pre>
</div>

使用该方法加载tabview、treeview两个模块，而tabview会去依赖animate模块，由于模块返回的都是对象，那我们可以new一个对象去调用加载模块中的方法和属性！

在main.js,我们需要去配置一下模块的路径，就那上面例子来说，需要配置一下几个模块的路径，如下：

<div class="cnblogs_code">
<pre><span style="color: #000000;">require.config({
　　　paths: {
　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">tabview</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">js/tabview</span><span style="color: #800000;">"</span><span style="color: #000000;">,
　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">animate</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">js/animate</span><span style="color: #800000;">"</span><span style="color: #000000;">,
　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">treeview</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">js/treeview</span><span style="color: #800000;">"</span><span style="color: #000000;">
　　　}
　});

</span><span style="color: #008000;">//</span><span style="color: #008000;">另一种则是直接改变基目录（baseUrl）。后缀.js可以省略</span>
<span style="color: #000000;">require.config({
　　　　baseUrl: </span><span style="color: #800000;">"</span><span style="color: #800000;">js/lib</span><span style="color: #800000;">"</span><span style="color: #000000;">,
　　　　paths: {
　　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">jquery</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">jquery.min</span><span style="color: #800000;">"</span><span style="color: #000000;">,
　　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">underscore</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">underscore.min</span><span style="color: #800000;">"</span><span style="color: #000000;">,
　　　　　　</span><span style="color: #800000;">"</span><span style="color: #800000;">backbone</span><span style="color: #800000;">"</span>: <span style="color: #800000;">"</span><span style="color: #800000;">backbone.min</span><span style="color: #800000;">"</span><span style="color: #000000;">
　　　　}
});</span></pre>
</div>

这样require.js便可以很灵活地使用来进行模块化管理了，这里有一个基于require.js去搭建一个web组件（弹窗的demo）,很值得学习一下！可以去看看！github地址：[https://github.com/xiaobinwu/require.js-Popup-window-](https://github.com/xiaobinwu/require.js-Popup-window-)

参考资料：[RequireJS和AMD规范](http://javascript.ruanyifeng.com/tool/requirejs.html)