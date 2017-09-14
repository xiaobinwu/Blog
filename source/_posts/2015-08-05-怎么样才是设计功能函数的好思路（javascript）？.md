---
title: 怎么样才是设计功能函数的好思路（javascript）？
tags: []
date: 2015-08-05 10:53:00
---

在js里面，对于函数的调用，实际上也是也是面向对象的思路，于是写好js函数，也是考核面向对象设计的能力，同时也必须考虑到如何实现高内聚和低耦合，拿一个例子来说，现在的需求是这样的，实现个投资进度框，就是如图所示：![](http://images0.cnblogs.com/blog2015/720690/201508/051008587985937.png)总共分四步来走，第一步&ldquo;创建订单中&rdquo;，成功改变提示信息&ldquo;创建订单成功！&rdquo;，显示![](http://images0.cnblogs.com/blog2015/720690/201508/051012594865772.png)，不成功改变提示信息&ldquo;创建订单失败！&rdquo;,显示![](http://images0.cnblogs.com/blog2015/720690/201508/051015158143258.png)，依次下去第二步，第三步，第四步！

<div class="para">我的dom结构是这样的[http://t.cn/RUbL4rP](http://t.cn/RUbL4rP)：</div>
<div class="para">
<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">&lt;!--投资操作进度tip--&gt;
&lt;div class="invest_progress_tip"&gt;
&lt;div class="progress_tip_title text-center"&gt;
&lt;div class="la-timer la-2x" &gt;
&lt;div&gt;&lt;/div&gt;
&lt;/div&gt;
&lt;span&gt;小羊正在拼命地处理中&middot;&middot;&middot;&lt;/span&gt;
&lt;/div&gt;
&lt;div class="progress_tip_content"&gt;

&lt;/div&gt;
&lt;/div&gt;
</pre>
</div>

&nbsp;

&lt;!--投资操作进度tip//--&gt;

那就是这样的，我需要做的就是设计一个函数，对其调用，依次传参，然后对其&lt;div class="progress_tip_content"&gt;&lt;/div&gt;添加dom节点，添加每个实现步骤。那如何设计这个函数勒？刚开始，我是依照三种状态来设计这个函数的，flag为0,1,2。0为初始化，1为成功，2为不成功，于是怎么一个函数就出来：

<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">/*
调用说明
show_p_tip({
step:"createOrder", //步骤名称，名称任意
flag: 0, // 0=状态未显示 1=状态已显示（成功） 2=状态已显示（未成功）
msg:'正在创建订单&middot;&middot;&middot; ', //提示信息
is_begin: true, //提示框初始化，用于第一步操作,第一步必须初始化,前提flag必为0
is_success: true, //成功支付的提示（成功支付按钮）,用于最后一步成功结果，,前提flag必为1
param: object对象 //如 {is_NoAuth:true, url:'http://www.baidu.com', tradeData:'hahahhaha', method:'post'} is_NoAuth必为true 前提flag必为2
});

*/

function show_p_tip(object){

switch(object.flag){
case 0:
if(object.is_begin){
$(".invest_tip .invest_tip_close").removeClass('close_window');
$(".invest_tip").css('z-index',9996);
$(".invest_progress_tip").center().fadeIn();
}
$(".invest_progress_tip .progress_tip_content").append('&lt;div id="div_'+object.step+'"&gt;&lt;span id="span_'+object.step+'"&gt;'+object.msg+'&lt;/span&gt;&lt;i class="p-icon p-1"&gt;&lt;/i&gt;&lt;/div&gt;'); 
break;

case 1:
if(object.msg!=''){
$(".invest_progress_tip .progress_tip_content #span_"+object.step).html(object.msg);
}	
$(".invest_progress_tip .progress_tip_content &gt; div#div_"+object.step+" .p-icon").fadeIn(); 
if(object.is_success){ 
$(".invest_progress_tip .progress_tip_content").append('&lt;div class="invest_success_result mt15 text-center"&gt;&lt;a href="javascript:void(0);" class="invest_result_btn btn btn-primary btn-block"&gt;完成支付&lt;/a&gt;&lt;/div&gt;');
}	
break;

case 2:
if(object.msg!=''){
$(".invest_progress_tip .progress_tip_content #span_"+object.step).html(object.msg);
}	
$(".invest_progress_tip .progress_tip_content &gt; div#div_"+object.step+" .p-icon").addClass('p-2');
$(".invest_progress_tip .progress_tip_content &gt; div#div_"+object.step+" .p-icon").fadeIn(); 
if(object.param.is_NoAuth){ 
var form = createForm(object.param);
$(".invest_progress_tip .progress_tip_content").append('&lt;div class="invest_noauth_tip mt5"&gt;点击支付按钮前往&lt;span class="text-primary"&gt;一麻袋&lt;/span&gt;进行支付操作&lt;/div&gt;'); 
$(".invest_progress_tip .progress_tip_content .invest_noauth_tip").append(form);
}	
break;

}

if(object.tip_msg&amp;&amp;object.tip_msg!=''){ 
$(".invest_progress_tip .progress_tip_title .la-timer&gt;div").addClass('success');	
$(".invest_progress_tip .progress_tip_title span").text(object.tip_msg); 
}

}
</pre>
</div>

&nbsp;

<span style="line-height: 1.5;">那么可以看出来，使用flag作为参考依据，于是就会出现很多问题，代码出现了很多问题，比如</span>

状态为flag为1，成功的时候，我要求显示&rdquo;完成支付&ldquo;的按钮，需要在flag为1的前提下，添加个参数is_success;

状态为flag为0,显示初始化的时候，需要在第一步做些其他操作（初始化），又要传入参数is_begin来保证第一步可以实现某个操作

虽然功能可以实现，但是代码相当冗余，可扩展性不好，相当不智能！

于是另外一种方法出来了，

<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">/*
调用说明
show_p_tip({
action:'_init', //动作名
name: 'tag1', //标识名
msg:'正在创建订单...', //提示信息
status: 1, // 0错误 1正确
form: object对象 //如 {url:'http://www.baidu.com', tradeData:'hahahhaha', method:'post'}
});
eg:

show_p_tip({ action:'_init',msg:'小羊正在疯狂处理中！'});

show_p_tip({ action:'_tips',name:'tag1',msg:'正在创建订单...'});
show_p_tip({ action:'_tips',name:'tag1',msg:'创建订单成功！',status:1});
show_p_tip({ action:'_tips',name:'tag1',msg:'现金券下单失败！',status:0});

show_p_tip({ action:'_jump',form:{url:'http://www.baidu.com', tradeData:'hahahhaha', method:'post'}});

show_p_tip({ action:'_complete'});

show_p_tip({ action:'_end'});
*/
function show_p_tip(object){
switch(object.action){
//初始化
case '_init':
var msg = object.msg?object.msg:'小羊正在拼命地处理中！';
$('.invest_progress_tip').attr('rel',1);
$('.invest_progress_tip .progress_tip_title span').text(msg);
$(".invest_tip .invest_tip_close").removeClass('close_window');
$(".invest_tip").css('z-index',9996);
$(".invest_progress_tip").center().fadeIn();
break;
//流程提示
case '_tips':
if($('.invest_progress_tip').attr('rel')!='1'){
show_p_tip({action:'_init'});
}
if(!$('#div_'+object.name).length){
$(".invest_progress_tip .progress_tip_content").append('&lt;div id="div_'+object.name+'"&gt;&lt;span id="span_'+object.name+'"&gt;'+object.msg+'&lt;/span&gt;&lt;i class="p-icon p-1"&gt;&lt;/i&gt;&lt;/div&gt;'); 
} 
$("#span_"+object.name).html(object.msg);
if(object.status==1) $("#div_"+object.name+" .p-icon").fadeIn();
if(object.status==0) $("#div_"+object.name+" .p-icon").addClass('p-2').fadeIn();
break;
//跳转支付
case '_jump':
var form = createForm(object.form);
$(".invest_progress_tip .progress_tip_content").append('&lt;div class="invest_noauth_tip mt5"&gt;点击支付按钮前往&lt;span class="text-primary"&gt;一麻袋&lt;/span&gt;进行支付操作&lt;/div&gt;'); 
$(".invest_progress_tip .progress_tip_content .invest_noauth_tip").append(form);
break;
//完成
case '_complete':
var msg = object.msg?object.msg:'完成支付';
$(".invest_progress_tip .progress_tip_content").append('&lt;div class="invest_success_result mt15 text-center"&gt;&lt;a href="javascript:void(0);" class="invest_result_btn btn btn-primary btn-block"&gt;'+msg+'&lt;/a&gt;&lt;/div&gt;');
break;
//结束
case '_end':
var msg = object.msg?object.msg:'小羊保证完成任务！';
$(".invest_progress_tip .progress_tip_title span").text(msg);
$(".invest_progress_tip .progress_tip_title .la-timer&gt;div").addClass('success'); 
break;
}	
}
</pre>
</div>

&nbsp;

</div>
<div class="para">&nbsp;这种方式，是以action来做参考依据的，分为5种情况，分别是_init（初始化），_tips（流程提示），_jump（跳转支付），_complete（完成支付），_end（结束），然后再使用递归，这样的话就划分了5个模块来处理这样的功能，_init（初始化），可以在第一步做递归调用，每一步都可以使用_tips（流程提示），即可随时改变状态，提示信息，有可以判断是否初始化，而决定是否递归调用_init（初始化），有可以处理其他特殊情况，比如第一步需要初始化操作，最后一步成功需要显示&rdquo;完成支付&ldquo;按钮，都是作为_jump（跳转支付），_complete（完成支付）来处理。代码不冗余了，可扩展性强了！条例清晰多了！</div>
<div class="para">&nbsp;</div>
<div class="para"><span style="color: #ff0000;">总结：在写js功能处理函数时，正确的思想是根据此功能将其划分成多个模块部分，通用模块，初始化模块，结束模块，特殊模块，用好递归等编程思想，这样的思路可以有效的写出好的代码！come on！！！bin！</span></div>
<div class="para">&nbsp;</div>