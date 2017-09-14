---
title: toolTip(用svg制作出富有动态的对话框)
tags: []
date: 2015-07-31 10:38:00
---

昨晚看了用svg如何制作富有动态的tooltip，于是今天就心血来潮学着做一下，于是也成功做出来，也明白其中的原理，收获颇多阿！接下来要多去学习svg，这是个好东西。

这其中也注意了一些平时纠结的细节应该怎么去做(演示：http://www.live086.cn/toolTip/)，比如：

 &lt;article&gt;
            &lt;section id="sound1"&gt;
            &lt;/section&gt;
            &lt;section id="sound2"&gt;
            &lt;/section&gt;
        &lt;/article&gt;

article标签长度为600px,section 分别是300px，然后设置其为display:inline-block;然后是下面的效果：

[http://t.cn/RUbL4rP](http://t.cn/RUbL4rP)

![](http://images0.cnblogs.com/blog2015/720690/201507/161802241415251.png)

本来按常理来说的话，应该是头像水平排列，这是因为display:inline-block;会将article标签和section标签之间空白渲染成空格，空格展位，所以会导致图片不在同一排，解决的办法是给article标签和section标签添加如下的css代码：

 article{ 
                width:600px;
                margin:200px;
                <span style="background-color: #888888;">font-size:0;</span>
              }
              article section{ 
                display:inline-block;
                width:300px;
                font-size:14px;
                position:relative;
              }

于是空白去掉了！

另外对于svg的web图像，我们可以对其进行修改，使其图像的样式可进行修改，它的格式大概如下（举一例子）：

<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!-- Generator: Adobe Illustrator 17.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0) --&gt;
&lt;!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd"&gt;
&lt;svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
width="600px" height="300px" viewBox="0 0 600 300" enable-background="new 0 0 600 300" xml:space="preserve"&gt;
&lt;polygon points="89.571,6.648 513.333,6.648 590.25,75.342 553.002,215.306 313.065,273.358 300,293.352 288.876,272.71 
48.936,215.306 9.75,75.342 "/&gt;
&lt;/svg&gt;
</pre>
</div>

&nbsp;

于是我们不可能将其引入到html文件里面，如果说有很多这种svg图像，修改起来很麻烦！

于是使用的是ajax来加载这个图片：

html的dom：&lt;svg data-src="bubble1.svg" width="280" height="140"&gt;&lt;/svg&gt;

 // 问题二：对于svg图像我们要如何引入，不可能将整个svg都引入吧，不便于修改编辑
                 // 技巧二：使用js进行加载 
                  $('svg[data-src]').each(function(index, svg) {
                    var src = $(svg).data('src'); //data用于获取data-*属性的路径
                    $.ajax({
                        url: src,
                        dataType: 'xml',
                        success: function(content) {
                            var doc = content.documentElement;
                            $(doc).attr({
                                width: $(svg).attr('width'),
                                height: $(svg).attr('height')
                            });
                            $(svg).after(doc).remove();
                        }
                    })
                });

还有对于图片的描边动画效果，这里又怎么个好的方法，只针对svg图像：

使用stroke-dasharray（虚线描边，可以不断尝试，使其调至适应大小，完成实现整个描边的效果）stroke-dashoffset（虚线间隔，调至整个svg没有描边的效果），然后使用transition实现这个动画

最终效果（如图，没有在线演示，动画效果出不来，不过下面贴的代码直接复制，再去下载两个svg图片和头像就可以使用）

![](http://images0.cnblogs.com/blog2015/720690/201507/162209448919584.png)![](http://images0.cnblogs.com/blog2015/720690/201507/162209584383247.png)

代码如下：

<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">&lt;!DOCTYPE html&gt;
&lt;html lang="zh-cn"&gt;
&lt;head&gt;
&lt;title&gt;toolTip聊天对话框制作&lt;/title&gt;
&lt;meta charset="utf-8"/&gt;
&lt;meta name="keywords" content="" /&gt;
&lt;meta name="description" content="" /&gt;
&lt;script type="text/javascript" src="jquery.js"&gt;&lt;/script&gt; 
&lt;style type="text/css"&gt;
h1{ 
color:red;
font-size:18px;
}
article{ 
width:600px;
margin:200px;
font-size:0;
}
article section{ 
/*问题一：对于display:inline-block;会出现两个section无法并排排列，由于使用此属性会将article与section之间的空白处渲染成空格，于是无法并排*/
/*技巧一： 父元素设置 font-size:0;清除空白*/
display:inline-block;
width:300px;
font-size:14px;
position:relative;
}
.text-center{ 
text-align:center;
}
#sound1,#sound2{ 
cursor:pointer; 
}
#sound1 img,#sound2 img{ 
width:100px;
height:100px;
border-radius:100%;
}
.sound_1,.sound_2{ 
position:absolute;
top:-104px;
width:200px;
height:100px;
box-sizing: border-box;
opacity:1;
}
.sound_2{ 
padding:28px;
}
.sound_1{ 
padding: 25px 68px 25px 30px;
left: -150px;
top: -134px;
width: 280px;
height: 140px;
}
.sound_1 svg ,.sound_2 svg{ 
position:absolute;
top:0;
left:0;
}
.sound_1 p,.sound_2 p{ 
position:relative;
margin:0;
color:#444;
font-size:12px;
} 
.sound_1 svg path, .sound_2 svg polygon{
fill:#fff;/*填充的颜色*/
stroke:red;/*描边的颜色*/
stroke-width: 6px;/*边的宽度*/
}
.sound_1 svg #path1 {
transform: scale(0, 0);
transform-origin: center;
opacity: 0;
transition-duration: .3s;
transition-delay: 0;
}
.sound_1 svg #path2 {
transform: scale(0, 0);
transform-origin: center;
opacity: 0;
transition-duration: .3s;
transition-delay: .1s;
}
.sound_1 svg #path3 {
transform: scale(0, 0);
transform-origin: center;
opacity: 0;
transition-duration: .3s;
transition-delay: .2s;
}
.sound_1 svg #path4 {
transform: scale(0, 0);
transform-origin: center;
opacity: 0;
transition-duration: .3s;
transition-delay: .25s;
} 
.sound_1 p {
transition: .2s .35s;
opacity: 0;
transform: translate(0, -10px);
} 
#sound1:hover .sound_1 svg #path1,#sound1:hover .sound_1 svg #path2,#sound1:hover .sound_1 svg #path3,#sound1:hover .sound_1 svg #path4{ 
transform: scale(1, 1);
opacity: 1;
transition-delay: 0;
} 
#sound1:hover .sound_1 p{ 
opacity: 1;
transform: translate(0, 0);
} 
/*问题三：对于图片的描边动画效果，这里又怎么个好的方法，只针对svg图像*/
/*技巧三：使用stroke-dasharray（虚线描边，可以不断尝试，使其调至适应大小，实现描边的效果）stroke-dashoffset（虚线间隔，调至整个svg没有描边的效果），然后使用transition实现这个动画 */ 
.sound_2 svg polygon{ 
stroke-dasharray: 1500;
stroke-dashoffset: 1500;
fill-opacity: 0;
transition: .6s;
}
.sound_2 p {
transition: .4s;
transform: scale(-0.5);
opacity: 0;
transform: translate(0, -10px);
} 
#sound2:hover .sound_2 svg polygon{ 
stroke-dashoffset: 0;
fill-opacity: 1;
}
#sound2:hover .sound_2 p {
transform: scale(0);
opacity: 1;
transform: translate(0, 0);
} 
&lt;/style&gt;
&lt;/head&gt; 
&lt;body&gt;

&lt;h1&gt;toolTip聊天对话框制作&lt;/h1&gt;

&lt;article&gt;
&lt;section id="sound1"&gt;
&lt;div class="text-center"&gt;&lt;img src="nan.jpg" /&gt;&lt;/div&gt;
&lt;p class="text-center"&gt;韩国正太&lt;/p&gt;
&lt;div class="sound_1"&gt;
&lt;svg data-src="bubble1.svg" width="280" height="140"&gt;&lt;/svg&gt;
&lt;p&gt;听说优衣库的试衣间已全面升级，空间大小扩充一倍，精装修，同时四面都安有镜子，方便无死角录像呢，要去试一下不，美女！&lt;/p&gt;
&lt;/div&gt;
&lt;/section&gt;
&lt;section id="sound2"&gt;
&lt;div class="text-center"&gt;&lt;img src="nv.jpg" /&gt; &lt;/div&gt;
&lt;p class="text-center"&gt;优衣库美女&lt;/p&gt;
&lt;div class="sound_2"&gt;
&lt;svg data-src="bubble2.svg" width="200" height="100"&gt;&lt;/svg&gt;
&lt;p&gt;听起来就很刺激，那走，帅哥，准备家伙，go！&lt;/p&gt;
&lt;/div&gt;
&lt;/section&gt;
&lt;/article&gt;

&lt;script type="text/javascript"&gt;
$(document).ready(function() {
// 问题二：对于svg图像我们要如何引入，不可能将整个svg都引入吧，不便于修改编辑
// 技巧二：使用js进行加载 
$('svg[data-src]').each(function(index, svg) {
var src = $(svg).data('src'); //data用于获取data-*属性的路径
$.ajax({
url: src,
dataType: 'xml',
success: function(content) {
var doc = content.documentElement;
$(doc).attr({
width: $(svg).attr('width'),
height: $(svg).attr('height')
});
$(svg).after(doc).remove();
}
})
});
})
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>
</div>

&nbsp;

<span style="line-height: 1.5;">&nbsp;</span>