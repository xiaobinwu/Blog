---
title: 使用git管理github上的项目
tags: []
date: 2015-10-06 16:45:00
---

使用git可以把我们的项目代码上传到github上面去，方便自己管理，如何使用git？觉得是每位程序猿所必需要有的谋生技能，所以在此记录一下自己学会使用的这个过程：

一、需要注册github账号，这样就可以在自己的github上面创建仓库（Create a&nbsp;New Repository）了，填好一些配置信息，然后便可以点击"Create Repository"按钮了！[http://t.cn/RUbL4rP](http://t.cn/RUbL4rP)如图：

&nbsp;![](http://images2015.cnblogs.com/blog/720690/201510/720690-20151006155503596-399577792.png)

二、安装客户端tortoiseGit(小乌龟)，于是我们便可以右键Git Init Here(初始化本地仓库)，然后会出现.git文件，同时也可以Git Bash进入git命令行，将项目代码上传至github上面创建的对应的仓库。

三、配置Git

　　1、首先需要在本地创建ssh key（可以理解创建密钥文件）

<div>　　　&nbsp; $** ssh-keygen -t rsa -C "[your_email@youremail.com](http://blog.sina.com.cn/s/blog_63eb3eec0101cf6x.htmlmailto:your_email@youremail.com)"**&nbsp; //双引号里面是自己的邮箱，需要是自己在github上面注册的邮箱，这一操作后要求确认路径和输入密码，密码建议与github密码一致，这样比较好记！一路回车键，成功的话，根据命令显示的地址找出.ssh文件夹，进去，找到id_rsa.pub，复制里面的key，登录github，进入**Setting -&gt;&nbsp; SSH keys**，点击Add SSH Key，将复制的key粘贴进去，title随便填！</div>
<div>&nbsp;</div>
<div>　　2、为了验证是否成功，在git bash输入 $ **ssh -T&nbsp;[git@github.com](http://blog.sina.com.cn/s/blog_63eb3eec0101cf6x.htmlmailto:git@github.com)&nbsp;**，如果是第一次的话，会提示是否continue，输入yes，会看到**You&rsquo;ve successfully authenticated, but GitHub does not provide shell access**，那么证明成功连接github！</div>
<div>&nbsp;</div>
<div>　　3、接下来还需要配置一下username和email，之后每次commit都会使用到的：　</div>
<div>
<div>　　　&nbsp; $ **git config --global user.name "your name"** //需要和github上名称一致</div>
<div>　　　&nbsp; $ **git config --global user.email "[your_email@youremail.com](http://blog.sina.com.cn/s/blog_63eb3eec0101cf6x.htmlmailto:your_email@youremail.com)"** //需要和github上注册邮箱一致</div>
<div>&nbsp;</div>
<div>四、Git常用命令行　</div>
<div>　　git clone ...&nbsp; //克隆别人的项目</div>
<div>
<div>&nbsp;&nbsp;&nbsp;&nbsp; 创建一个项目名为angular文件夹
&nbsp;&nbsp;&nbsp;&nbsp; 进入这个angular项目
&nbsp;&nbsp;&nbsp;&nbsp; $ git init&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //初始化&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp; $ git add README.md &nbsp; &nbsp; &nbsp; &nbsp;//更新README文件</div>
<div>　&nbsp; $ git add *&nbsp;//更新所有文件
&nbsp;&nbsp;&nbsp;&nbsp; $ git commit -m 'first commit'&nbsp;&nbsp;&nbsp;&nbsp; //提交更新，并注释信息&ldquo;one commit&rdquo;，第一次提交
&nbsp;&nbsp;&nbsp;&nbsp; $ git remote add origin&nbsp;[git@github.com:xiaobin5201314/angular.git](http://blog.csdn.net/steven6977/article/details/11268675mailto:git@github.com:defnngj/hello-world.git)&nbsp;&nbsp;&nbsp;&nbsp; //第一次需要连接远程github项目
&nbsp;&nbsp;&nbsp;&nbsp; $ git push -u origin master&nbsp;&nbsp;&nbsp; &nbsp;//将本地项目更新到github项目上去，或是（git push origin master）</div>
<div>　 &nbsp;$ git pull -u origin master&nbsp;&nbsp;&nbsp; &nbsp;//将github项目更新到本地，或是（git pull origin master）</div>
<div>　 &nbsp;$&nbsp;git checkout -b feature_x &nbsp; //创建一个叫做&ldquo;feature_x&rdquo;的分支，并切换过去</div>
<div>　 &nbsp;$&nbsp;git checkout master &nbsp;//切换回主分支</div>
<div>　 &nbsp;$ git branch -d feature_x &nbsp;//把新建的分支删掉</div>
<div>　 &nbsp;$&nbsp;git push origin &lt;branch&gt; //将分支推送到远端仓库_
_</div>
<div>　 &nbsp;$ &nbsp;git merge &lt;branch&gt; //将某个分支合并到master</div>
<div>&nbsp;</div>
<div>五、常见错误：</div>
<div>
<div>

&nbsp;如果输入$ git remote add origin&nbsp;[git@github.com:xiaobin5201314（github帐号名）/gitdemo（项目名）.git](http://blog.csdn.net/steven6977/article/details/11268675mailto:git@github.com:djqiang/gitdemo.git)&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;提示出错信息：fatal: remote origin already exists.

&nbsp;&nbsp;&nbsp;&nbsp;解决办法如下：

&nbsp;&nbsp;&nbsp;&nbsp;1、先输入$ git remote rm origin

&nbsp;&nbsp;&nbsp;&nbsp;2、再输入$ git remote add origin&nbsp;[git@github.com:xiaobin5201314/gitdemo.git](http://blog.csdn.net/steven6977/article/details/11268675mailto:git@github.com:djqiang/gitdemo.git)&nbsp;就不会报错了！

&nbsp;&nbsp;&nbsp;&nbsp;3、如果输入$ git remote rm origin&nbsp;还是报错的话，error: Could not remove config section 'remote.origin'. 我们需要修改gitconfig文件的内容

&nbsp;&nbsp;&nbsp; 4、找到你的github的安装路径，我的是C:\Users\ASUS\AppData\Local\GitHub\angular_d14f7551eeb4aea0e4ae9fcd3358bd96420bb5c8\etc

&nbsp;&nbsp;&nbsp; 5、找到一个名为gitconfig的文件，打开它把里面的`[remote "origin"]那一行`删掉就好了！

&nbsp;

&nbsp;&nbsp;&nbsp; 如果输入$ ssh -T&nbsp;[git@github.com](http://blog.csdn.net/steven6977/article/details/11268675mailto:git@github.com)
&nbsp;&nbsp;&nbsp; 出现错误提示：Permission denied (publickey).因为新生成的key不能加入ssh就会导致连接不上github，可以重新生成一个。

&nbsp;&nbsp;&nbsp; 解决办法如下：

&nbsp;&nbsp;&nbsp; 1、先输入$ ssh-agent，再输入$ ssh-add ~/.ssh/id_key，这样就可以了。

&nbsp;&nbsp;&nbsp; 2、如果还是不行的话，输入ssh-add ~/.ssh/id_key 命令后出现报错Could not open a connection to your authentication agent.解决方法是key用Git Gui的ssh工具生成，这样生成的时候key就直接保存在ssh中了，不需要再ssh-add命令加入了，其它的user，token等配置都用命令行来做。

&nbsp;&nbsp;&nbsp; 3、最好检查一下在你复制id_rsa.pub文件的内容时有没有产生多余的空格或空行，有些编辑器会帮你添加这些的。

&nbsp;

&nbsp;&nbsp;&nbsp; 如果输入$ git push origin master

&nbsp;&nbsp;&nbsp; 提示出错信息：error:failed to push som refs to .......

&nbsp;&nbsp;&nbsp; 解决办法如下：

&nbsp;&nbsp;&nbsp; 1、先输入$ git pull origin master //先把远程服务器github上面的文件拉下来

&nbsp;&nbsp;&nbsp; 2、再输入$ git push origin master

&nbsp;&nbsp;&nbsp; 3、如果出现报错 fatal: Couldn't find remote ref master或者fatal: 'origin' does not appear to be a git repository以及fatal: Could not read from remote repository.

&nbsp;&nbsp;&nbsp; 4、则需要重新输入$ git remote add origin[git@github.com:xiaobin5201314/gitdemo.git](http://blog.csdn.net/steven6977/article/details/11268675mailto:git@github.com:djqiang/gitdemo.git)

&nbsp;

</div>

</div>

</div>

</div>
<div>六、README.md也可以怎么编写</div>
<div>&nbsp;</div>
<div>　&nbsp; &nbsp;README.md不单单可以写文字说明，还可以控制样式，显示图片，列表等有趣的操作，使用的是一种MarkDown的标签语言，十分简单，感兴趣可以到[原来Github上的README.md文件这么有意思&mdash;&mdash;Markdown语言详解](http://www.kuqin.com/shuoit/20141125/343459.html)去阅读，这篇文章有着很详细的介绍，里面也介绍了两个比较实用的在线编写README.md的工具！</div>