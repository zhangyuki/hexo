---
title: 使用Hexo + Github Pages搭建个人独立博客
---

一：hexo系统环境配置

	要使用Hexo，需要在你的系统中支持Nodejs以及Git，如果还没有，那就开始安装吧！

	安装Node.js

	下载Node.js
	参考地址：安装Node.js

	安装Git

	下载地址：http://git-scm.com/download/

	安装Hexo

		$ cd d:/hexo
		$ npm install hexo-cli -g
		$ hexo init blog
		$ cd blog
		$ npm install
		$ hexo g # 或者hexo generate
		$ hexo s # 或者hexo server，可以在http://localhost:4000/ 查看
		这里有必要提下Hexo常用的几个命令：

	1、hexo generate (hexo g) 生成静态文件，会在当前目录下生成一个新的叫做public的文件夹/

	2、hexo server (hexo s) 启动本地web服务，用于博客的预览
	hexo deploy (hexo d)

	3、部署播客到远端（比如github, heroku等平台）

	另外还有其他几个常用命令：

	$ hexo new "postName" #新建文章
	$ hexo new page "pageName" #新建页面

常用简写

	$ hexo n == hexo new
	$ hexo g == hexo generate
	$ hexo s == hexo server
	$ hexo d == hexo deploy
	常用组合

	$ hexo d -g #生成部署
	$ hexo s -g #生成预览
	现在我们打开http://localhost:4000已经可以看到一篇内置的blog了。


目前我安装所用的本地环境如下：(可以通过hexo -v查看)

	hexo: 3.2.0
	hexo-cli: 1.0.1
	os: Windows_NT 6.3.9600 win32 x64
	http_parser: 2.5.2
	node: 4.4.1
	v8: 4.5.103.35
	uv: 1.8.0
	zlib: 1.2.8
	ares: 1.10.1-DEV
	icu: 56.1
	modules: 46
	openssl: 1.0.2g
Hexo主题设置

这里以主题yilia为例进行说明。

安装主题

	$ hexo clean
	$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
启用主题

	$ cd themes/yilia
	$ git pull
	$ hexo g # 生成
	$ hexo s # 启动本地web服务器
    现在打开http://localhost:4000，会看到我们已经应用了一个新的主题。

二：Github Pages设置

	什么是Github Pages

	GitHub Pages 本用于介绍托管在GitHub的项目，不过，由于他的空间免费稳定，用来做搭建一个博客再好不过了。

	每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。你可以通过http://username.github.io 来访问你的个人主页。

	这里特别提醒一下，需要注意的个人主页的网站内容是在master分支下的。

	创建自己的Github Pages

	注册GitHub及使用Github Pages的过程已经有很多文章讲过，在此不再详述，可以参考：

	如何搭建一个独立博客——简明Github Pages与Hexo教程

	在这里我创建了一个github repo叫做 dushao103500.github.io 创建完成之后，需要有一次提交(git commit)操作，然后就可以通过链接http://dushao103500.github.io 访问了。（现在还没有内容，别着急）

三：部署Hexo到Github Pages

	这一步恐怕是最关键的一步了，让我们把在本地web环境下预览到的博客部署到github上，然后就可以直接通过http://dushao.github.io访问了。不过很多教程文章对这个步骤语焉不详，这里着重说下。

	首先需要明白所谓部署到github的原理

	1、之前步骤中在Github上创建的那个特别的repo（dushao103500.github.io）一个最大的特点就是其master中的html静态文件，可以通过链接http://dushao103500.github.io来直接访问。

	2、Hexo -g 会生成一个静态网站（第一次会生成一个public目录），这个静态文件可以直接访问。

	3、需要将hexo生成的静态网站，提交(git commit)到github上。

	明白了原理，怎么做自然就清晰了。

	使用hexo deploy部署

	hexo deploy可以部署到很多平台，具体可以参考这个链接. 如果部署到github，需要在配置文件_config.xml中作如下修改：

	deploy:
	  type: git
	  repo: git@github.com:jiji262/jiji262.github.io.git
	  branch: master
	然后在命令行中执行

	hexo d
	即可完成部署。

踩坑提醒

（1）注意需要提前安装一个扩展：

$ npm install hexo-deployer-git --save

（2）如果出现下面这样的错误：

	Permission denied (publickey).
	fatal: Could not read from remote repository.
	Please make sure you have the correct access rights
	and the repository exists.
	则是因为没有设置好public key所致。
	在本机生成public key(参考github帮助)：

	＃ssh-keygen -t rsa -b 4096 -C "xxx@xxx.com"
	然后在#user_id/.ssh目录下会生成两个文件，id_rsa.pub和id_rsa.
	然后登陆github，在SSH设置页面添加上刚才的public key文件也就是id_rsa.pub的内容即可。

	使用git命令行部署（optional）

	不幸的是，上述命令虽然简单方便，但是偶尔会有莫名其妙的问题出现，因此，我们也可以追本溯源，使用git命令来完成部署的工作。

	clone github repo

	$ cd d:/hexo/blog
	$ git clone https://github.com/jiji262/jiji262.github.io.git .deploy/jiji262.github.io
	将我们之前创建的repo克隆到本地，新建一个目录叫做.deploy用于存放克隆的代码。

	创建一个deploy脚本文件

	hexo generate
	cp -R public/* .deploy/jiji262.github.io
	cd .deploy/jiji262.github.io
	git add .
	git commit -m “update”
	git push origin master
	简单解释一下，hexo generate生成public文件夹下的新内容，然后将其拷贝至jiji262.github.io的git目录下，然后使用git commit命令提交代码到jiji262.github.io这个repo的master branch上。

	需要部署的时候，执行这段脚本就可以了（比如可以将其保存为deploy.sh）。执行过程中可能需要让你输入Github账户的用户名及密码，按照提示操作即可。

	Hexo 主题配置

	每个不同的主题会需要不同的配置，主题配置文件在主题目录下的_config.yml。
	以我们使用的yilia主题为例，其提供如下的配置项theme\yilia_config.yml：

	# Header
	menu:
	  主页: /
	  所有文章: /archives
	  # 随笔: /tags/随笔

	# SubNav
	subnav:
	  github: "dushao103500"
	  weibo: "DanD逆光之处是暖伤"
	  rss: "#"
	  zhihu: "#"
	  #douban: "#"
	  #mail: "dushao1314@foxmail.com"
	  #facebook: "#"
	  #google: "#"
	  #twitter: "#"
	  #linkedin: "#"

	rss: /atom.xml

	# Content
	excerpt_link: more
	fancybox: true
	mathjax: true

	# Miscellaneous
	google_analytics: ''
	favicon: /favicon.png

	#你的头像url
	avatar: ""
	#是否开启分享
	share: true
	#是否开启多说评论，填写你在多说申请的项目名称 duoshuo: duoshuo-key
	#若使用disqus，请在博客config文件中填写disqus_shortname，并关闭多说评论
	duoshuo: true
	#是否开启云标签
	tagcloud: true

	#是否开启友情链接
	#不开启——
	#friends: false

	#是否开启“关于我”。
	#不开启——
	#aboutme: false
	#开启——
	aboutme: 我是谁，我从哪里来，我到哪里去？我就是我，是颜色不一样的吃货…
	四：其他高级使用技巧

	绑定独立域名

	购买域名

	在你的域名注册提供商那里配置DNS解析，获取GitHub的IP地址点击，进入source目录下，添加CNAME文件

	$ cd source/
	$ touch CNAME
	$ vim CNAME # 输入你的域名
	$ git add CNAME
	$ git commit -m "add CNAME"
	使用图床

	使用七牛云存储

	自己在github上搭建的图床，需要首先注册七牛账号使用。
	添加插件

	添加sitemap和feed插件

	$ npm install hexo-generator-feed
	$ npm install hexo-generator-sitemap
	修改_config.yml，增加以下内容

	# Extensions
	Plugins:
	- hexo-generator-feed
	- hexo-generator-sitemap
	#Feed Atom
	feed:
	  type: atom
	  path: atom.xml
	  limit: 20
	#sitemap
	sitemap:
	  path: sitemap.xml
	配完之后，就可以访问http://dushao103500.github.io，发现已经成功生成了。

	添加404公益页面

	GitHub Pages有提供制作404页面的指引：Custom 404 Pages。

	直接在根目录下创建自己的404.html或者404.md就可以。但是自定义404页面仅对绑定顶级域名的项目才起作用，GitHub默认分配的二级域名是不起作用的，使用hexo server在本机调试也是不起作用的。

	推荐使用腾讯公益404。

	添加about页面

	$ hexo new page "about"
	之后在\source\about\index.md目录下会生成一个index.md文件，打开输入个人信息即可，如果想要添加版权信息，可以在文件末尾添加：

	<div style="font-size:12px;border-bottom: #ddd 1px solid; BORDER-LEFT: #ddd 1px solid; BACKGROUND: #f6f6f6; HEIGHT: 120px; BORDER-TOP: #ddd 1px solid; BORDER-RIGHT: #ddd 1px solid">
	<div style="MARGIN-TOP: 10px; FLOAT: left; MARGIN-LEFT: 5px; MARGIN-RIGHT: 10px">
	<IMG alt="" src="https://avatars1.githubusercontent.com/u/168751?v=3&s=140" width=90 height=100>
	</div>
	<div style="LINE-HEIGHT: 200%; MARGIN-TOP: 10px; COLOR: #000000">
	本文链接：<a href="<%= post.link %>"><%= post.title %></a> <br/>
	作者： 
	<a href="http://dushao103500.github.io/">杜少</a> <br/>出处： 
	<a href="http://dushao103500.github.io/">http://jiji262.github.io/</a>
	<br/>本文基于<a target="_blank" title="Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)" href="http://creativecommons.org/licenses/by-sa/4.0/"> 知识共享署名-相同方式共享 4.0 </a>
	国际许可协议发布，欢迎转载，演绎或用于商业目的，但是必须保留本文的署名 
	<a href="http://jiji262.github.io/">杜少</a>及链接。
	</div>
	</div>
	添加Fork me on Github

	获取代码，选择你喜欢的代码添加到hexo/themes/yilia/layout/layout.ejs的末尾即可，注意要将代码里的you改成你的Github账号名。

	添加支付宝捐赠按钮及二维码支付

	支付宝捐赠按钮

	在D:\hexo\themes\yilia\layout_widget目录下新建一个zhifubao.ejs文件，内容如下：

	<p class="asidetitle">打赏他</p>
	<div>
	<form action="https://shenghuo.alipay.com/send/payment/fill.htm" method="POST" target="_blank" accept-charset="GBK">
	    <br/>
	    <input name="optEmail" type="hidden" value="your 支付宝账号" />
	    <input name="payAmount" type="hidden" value="默认捐赠金额(元)" />
	    <input id="title" name="title" type="hidden" value="博主，打赏你的！" />
	    <input name="memo" type="hidden" value="你Y加油，继续写博客！" />
	    <input name="pay" type="image" value="转账" src="http://7xig3q.com1.z0.glb.clouddn.com/alipay-donate-website.png" />
	</form>
	</div>
	添加完该文件之后，要在D:/hexo/themes/yilia/_config.yml文件中启用，如下所示，添加zhifubao

	widgets:
	- category
	- tag
	- links
	- tagcloud
	- zhifubao
	- rss
	二维码捐赠

	首先需要到这里获取你的支付宝账户的二维码图片，支付宝提供了自定义功能，可以添加自定义文字。

	我的二维码扫描捐赠添加在about页面，当然你也可以添加到其它页面，在D:\hexo\blog\source\about下有index.md打开，在适当位置添加

	<center>
	欢迎您捐赠本站，您的支持是我最大的动力！
	![][http://7xsxyo.com1.z0.glb.clouddn.com/2016/04/15/FoJ1F6Ht0CNaYuCdE2l52F-Fk9Vk202.png]
	</center>
	<br/>
	可以让图片居中显示，注意将图片链接地址换成你的即可。

	添加百度站内搜索

	点击进入，点击其它工具->站内检索->现在使用->新建搜索引擎->查看代码，将代码里的id值复制，打开/d/hexo/themes/jacman/_config.xml，配置成如下即可。

	baidu_search:     ## http://zn.baidu.com/
	  enable: true
	  id: "1433674487421172828" ## e.g. "783281470518440642"  for your baidu search id
	  site: http://zhannei.baidu.com/cse/search ## your can change to your site instead of the default
	使用不蒜子添加访客统计

	详情参考搞定你的网站计数，具体做法很简单，就是在你的themes/your themes/layout/_partial/footer.ejs底部加入这段脚本

	<script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
	然后在``中间添加如下统计信息即可

	本站总访问量 <span id="busuanzi_value_site_pv"></span> 次, 访客数 <span id="busuanzi_value_site_uv">
	不蒜子的官方服务网站是不蒜子，目前最大的弊端就是不开放注册，所以对于运行了一段时间的网站，不蒜子的数据都是从1开始，没办法设置，只有等后期开放注册之后，登入网站才能对统计计数进行设置。

	至此，Hexo+Github Pages搭建个人博客完成

	快去告诉你们的小伙伴吧
