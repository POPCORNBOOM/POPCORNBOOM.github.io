---
layout: post
title: 【小 工 具】BrickcomBreaker（BCB）摄像头弱口令漏洞利用
image: wointonetcam.jpg
date: 2021-03-05 23:57:00 +0200
tags: [工具,漏洞,校园,网络摄像头]
categories: BCB
---

写在前面，此软件提供的是一种对Brickcom网络摄像头网络客户端弱密码利用的解决方案，软件将会在Github上开源，以供大家学习参考。同时，呼吁各大高校能够重视内网摄像头安全，使用复杂密码保护网络摄像头，防止被恶意利用，也呼吁各大摄像头厂商完善摄像头安全系统，不让不法分子乘机利用。

***

<div class="article-holder"><p><span class="color-yellow-02">这是</span><span class="color-yellow-02 font-size-23"><strong>米花</strong></span><span class="color-yellow-02">第三次写专栏，如果有什么不足的地方，还是希望大家多多<strong>包涵</strong>并给出<strong>建议</strong>~</span></p><p><span class="color-yellow-02"></span></p><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><p><strong><span class="color-pink-03"><span class="font-size-23">写</span>在前面，此软件提供的是一种对Brickcom网络摄像头网络客户端弱密码利用的<span class="color-pink-03 font-size-20">解决方案</span>，软件将会在Github上开源，以供大家学习</span></strong><strong><span class="color-pink-03">参考。</span></strong><strong><span class="color-pink-03">同时，<span class="color-yellow-04 font-size-23">呼吁</span><span class="color-yellow-04">各大高校能够重视<span class="font-size-20">内网摄像头安全</span>，使用复杂密码保护网络摄像头，防止被<span class="font-size-20">恶意利用，<span class="color-yellow-04 font-size-23">也呼吁<span class="color-yellow-04 font-size-16">各大摄像头厂商完善摄像头安全系统，<span class="color-yellow-04 font-size-20">不让不法分子乘机利用</span>。</span></span></span></span></span></strong></p><p><span class="font-size-20"><strong><span class="color-pink-04">申明：本软件使用者所有操作带来的一切后果，开发者概不负责</span></strong></span></p><p><span class="color-yellow-02"></span></p><h1><strong>目录</strong></h1><p><span class="color-pink-02 font-size-20">这是这篇文章的目录，请根据需要阅读。</span><strong><span class="font-size-23"><br></span></strong></p><ul class=" list-paddingleft-2"><li><p>背景</p></li><li><p>解决方案</p></li><li><p>工具开发过程</p></li><li><p>使用BCB自动获取Brickcom摄像头管理员权限</p></li><li><p>下载测试版</p></li></ul><p>（每个<span class="color-pink-02"><strong>要点</strong></span>用<span class="color-default">两</span>只小电视括起来）</p><p><br></p><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><h1><span class="color-pink-02">背景</span></h1><ul class=" list-paddingleft-2"><li><h1>网&nbsp; &nbsp;管</h1></li></ul><p>在班上电脑偶然打开了网络界面，发现里面有个网络摄像头跟我在同一个c段！当时也没多想就抱着试一试的心态进去了。</p><p><br></p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/314fa1415b7feaaeb8f76554ffca7354b1ea1a9e.png@1320w_274h.webp" width="1011" height="210" data-size="14243" data-index="0" src="//i0.hdslb.com/bfs/article/314fa1415b7feaaeb8f76554ffca7354b1ea1a9e.png@1320w_274h.webp" class="loaded" style="width: 660px; height: 137px;"><figcaption class="caption">网络界面</figcaption></figure><ul class=" list-paddingleft-2"><li><h1>尝试<br></h1></li></ul><p>打开后发现是一台网络摄像头的web管理登入页面，试了试一个弱口令admin/admin，没想到就直接进去了！</p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/4004ef5de26c79f839317b67c829c786c018ebba.png@1320w_632h.webp" width="1869" height="895" data-size="952414" data-index="1" src="//i0.hdslb.com/bfs/article/4004ef5de26c79f839317b67c829c786c018ebba.png@1320w_632h.webp" class="loaded" style="width: 660px; height: 316px;"><figcaption class="caption">Brickcom摄像头Web管理登入界面</figcaption></figure><p>立马进行管理员用户创建&gt;为新用户获取一切权限&gt;尝试原用户降权</p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/dcd2a3fa40b8141ea158bb6e2311787bbed35326.png@1320w_636h.webp" width="1871" height="902" data-size="565291" data-index="2" src="//i0.hdslb.com/bfs/article/dcd2a3fa40b8141ea158bb6e2311787bbed35326.png@1320w_636h.webp" class="loaded" style="width: 660px; height: 318px;"><figcaption class="caption">尝试登入成功！</figcaption></figure><ul class=" list-paddingleft-2"><li><h1>研究</h1></li></ul><p>由于管理页面是在web上的。登入、创建/删除用户、获取权限等一切操作都是可以通过http请求的方式完成。</p><p>打开浏览器开发人员工具，在登入前开始对网络活动进行记录。记录下登入时发送的请求后，对其标头、Cookie和正文进行分析</p><p><br></p><ol class=" list-paddingleft-2"><li><p>&lt;Envelope&gt;</p></li><li><p>&lt;Header&gt;</p></li><li><p>&lt;<span class="color-purple-02">cmd</span>&gt;<span class="color-purple-04"><strong>UserLogin</strong></span>&lt;<span class="color-purple-02">/cmd</span>&gt;</p></li><li><p>&lt;cmd_type&gt;set&lt;/cmd_type&gt;</p></li><li><p>&lt;err_flag&gt;0&lt;/err_flag&gt;</p></li><li><p>&lt;channel&gt;0&lt;/channel&gt;</p></li><li><p>&lt;/Header&gt;</p></li><li><p>&lt;body&gt;</p></li><li><p>&lt;tmUserInfo_t&gt;</p></li><li><p>&lt;<span class="color-purple-02">szUserName</span>&gt;<strong><span class="color-purple-04">admin</span></strong>&lt;<span class="color-purple-02">/szUserName</span></p></li><li><p>&lt;<span class="color-purple-02">szPassword</span>&gt;<strong><span class="color-purple-04">21232F297A57A5A743894A0E4A801FC3</span></strong>&lt;<span class="color-purple-02">/szPassword</span>&gt;</p></li><li><p>&lt;/tmUserInfo_t&gt;</p></li><li><p>&lt;/body&gt;</p></li><li><p>&lt;/Envelope&gt;</p></li></ol><p>可以看到里面的&lt;<span class="color-purple-02">cmd</span>&gt;&lt;<span class="color-purple-02">/cmd</span>&gt;指的是登入命令、&lt;<span class="color-purple-02">szUserName</span>&gt;&lt;<span class="color-purple-02">/szUserName<span class="color-default">&gt;指的是登入用户名</span></span>&lt;<span class="color-purple-02">szPassword</span>&gt;&lt;<span class="color-purple-02">/szPassword</span>&gt;指的是登入密码的字符加密</p><p><br></p><figure class="img-box"><img class="cut-off-4 loaded" src="//i0.hdslb.com/bfs/article/db75225feabec8d8b64ee7d3c7165cd639554cbc.png"></figure><h1><span class="color-pink-02"></span><br></h1><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><h1><span class="color-pink-02">解决方案</span></h1><ul class=" list-paddingleft-2"><li><h1><span class="color-default">网页客户端</span></h1><p>针对网络摄像头在<strong><span class="color-pink-02">web页面</span></strong><span class="color-default">上</span>交互管理，摄像头初始的<strong><span class="color-pink-02">弱密码</span></strong>以及其通过<span class="color-pink-02"><strong>Web请求</strong></span>的方式发送命令，我想到了通过对指定摄像头IP发送请求的方式，<strong><span class="color-pink-02">直接</span></strong>创建新的用户并为新用户获取一切权限！</p></li><br><li><h1>操作简便</h1><p>考虑到未来使用的<span class="color-pink-02"><strong>简便性</strong></span>与<strong><span class="color-pink-02">直观性</span></strong>，我决定使用具有Win窗体界面的模板创建项目。</p></li><br></ul><ul class=" list-paddingleft-2"></ul><h1><br></h1><p><span class="color-pink-02"><br></span></p><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><h1><span class="color-pink-02"></span><span class="color-pink-02"><strong>工具开发过程</strong></span></h1><ul class=" list-paddingleft-2"><li><h1>创建</h1><p>依旧是拿手C#，所以还是用基于<strong><span class="color-pink-02">C</span><span style="text-decoration: line-through" class="color-pink-02"></span></strong><span style="text-decoration: line-through" class="color-gray-01">上艹下艹</span><span class="color-pink-04"><span style="text-decoration: line-through;"></span><span class="color-pink-02"><strong>#</strong><span class="color-default">和</span></span></span><strong><span class="color-pink-02">.NET</span></strong>框架的<strong><span class="color-pink-02">窗体应用</span></strong>模板创建项目<br></p></li></ul><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/064d30a687647496678825e4af35e03f8b96962a.png@1026w_164h.webp" width="513" height="82" data-size="6299" data-index="3" src="//static.hdslb.com/images/transparent.gif" style="width: 513px; height: 82px;"><figcaption class="caption"></figcaption></figure><p><br></p><ul class=" list-paddingleft-2"><li><h1>设计</h1></li><ul class=" list-paddingleft-2"><li><p><span class="font-size-16"><strong><span class="color-gray-02">主页面</span></strong></span></p><p>配上了<strong><span class="color-pink-02">自己画的学校地图</span></strong>，便于操作。看上去好像只能给自己的学校用，适用范围<strong><span class="color-pink-02">太小</span></strong>了，<span class="color-pink-04"><strong><span class="font-size-20">不过</span></strong></span>……</p></li></ul></ul><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/00c26a1f8819cd6dea1f6382acc378b3a8902e83.png@1320w_736h.webp" width="790" height="441" data-size="47475" data-index="4" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 368px;"><figcaption class="caption">主页面设计</figcaption></figure><p><br></p><ul class=" list-paddingleft-2"><li><p><span class="color-gray-02"><strong>“操作与破解”界面</strong></span></p><p>有了<strong><span class="color-pink-02">这个页面</span></strong>，只要你知道<span class="color-pink-02"><b>目标摄像头</b><span class="color-default">的</span></span><strong><span class="color-pink-02">网络位置（IP）</span></strong>，就可以直接对其进行弱密码攻击辣</p></li></ul><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/a5a5bdb415a61eda11a94e8fe68ba2504c189721.png@1320w_744h.webp" width="794" height="448" data-size="22193" data-index="5" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 372px;"><figcaption class="caption"></figcaption></figure><ul class=" list-paddingleft-2"><li><p><span class="color-gray-02"><strong>“设置与帮助”界面</strong></span></p><p>这是<span class="color-pink-02"><strong>初设计</strong></span>，设置部分还没想好要整点啥</p><p><span style="text-decoration: line-through" class="color-gray-01">ATE是我瞎编的</span></p></li></ul><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/f04c54ca1a1fe8d83e819fa0f7089fa1b94c9368.png@1320w_738h.webp" width="788" height="440" data-size="29263" data-index="6" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 369px;"><figcaption class="caption">“设置与帮助”界面设计</figcaption></figure><p><br></p><p><br></p><p><br></p><ul class=" list-paddingleft-2"><li><h1>编写</h1></li></ul><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/ad95d3368965866c8336384fa4ca9a43dcdf7e2b.png@592w_194h.webp" width="296" height="97" data-size="3992" data-index="7" src="//static.hdslb.com/images/transparent.gif" style="width: 296px; height: 97px;"><figcaption class="caption">USING</figcaption></figure><p>&nbsp;&nbsp;&nbsp;&nbsp;为控件添加脚本</p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/3f543d9247868af44b0d401d9623a81f33bf41f7.png@880w_136h.webp" width="440" height="68" data-size="3154" data-index="8" src="//static.hdslb.com/images/transparent.gif" style="width: 440px; height: 68px;"><figcaption class="caption">地图选中切换tab（其一）脚本</figcaption></figure><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/6e12f9ce248eb80b0047e955be640cbd987fcc84.png@1258w_522h.webp" width="629" height="261" data-size="13875" data-index="9" src="//static.hdslb.com/images/transparent.gif" style="width: 629px; height: 261px;"><figcaption class="caption">打开网页客户端按钮脚本</figcaption></figure><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/67a4c9a96588e7cc0736d32120ca76a43711dffa.png@1320w_580h.webp" width="1332" height="586" data-size="82074" data-index="10" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 290px;"><figcaption class="caption">一键完成按钮脚本</figcaption></figure><p>接下来是……</p><p><span class="color-pink-04 font-size-23"><strong><span class="font-size-23">完成！！！</span></strong></span></p><p><br></p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/1c2b2d2954089050e5306fb68bd7f1206b7e1a62.gif" width="382" height="240" data-size="83272" data-index="11" src="//static.hdslb.com/images/transparent.gif" style="width: 382px; height: 240px;"><figcaption class="caption"></figcaption></figure><p><span class="color-pink-04 font-size-23"><strong><span class="font-size-23"></span></strong></span><span class="color-gray-02">（把设置和帮助拆开了，窗口边框去掉了，用小红框代替关闭按钮）</span></p><figure class="img-box"><img class="cut-off-4 loaded" src="//i0.hdslb.com/bfs/article/db75225feabec8d8b64ee7d3c7165cd639554cbc.png"></figure><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><h1><span class="color-pink-02">使用BCB自动获取Brickcom摄像头管理员权限</span></h1><ol class=" list-paddingleft-2"><li><p><span class="color-pink-02">我们先用<strong><span class="color-blue-02">Goby</span></strong>对当前C段扫描一下</span></p></li></ol><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/4fe33c05d838a58c8618281b144acf447a130ca0.png@724w_362h.webp" width="362" height="181" data-size="11422" data-index="12" src="//static.hdslb.com/images/transparent.gif" style="width: 362px; height: 181px;"><figcaption class="caption"></figcaption></figure><p>这个C段就找到了17台这样的设备</p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/eb7c0269791b9ecc4717b39070abd322da284e4c.png@1320w_1196h.webp" width="667" height="604" data-size="73101" data-index="13" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 598px;"><figcaption class="caption"></figcaption></figure><p>在<strong><span class="color-pink-02">设置页面</span></strong>可以设置备用账号的账号密码哦</p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/e0e67afeaae5592e81d3a2911aa5f1ba2efc47a1.png@556w_440h.webp" width="278" height="220" data-size="10666" data-index="14" src="//static.hdslb.com/images/transparent.gif" style="width: 278px; height: 220px;"><figcaption class="caption"></figcaption></figure><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/79af406e006a418747d98e2ea060fa60683454f6.png@1320w_770h.webp" width="787" height="459" data-size="23147" data-index="15" src="//static.hdslb.com/images/transparent.gif" style="width: 660px; height: 385px;"><figcaption class="caption"></figcaption></figure><p>直接<span class="color-pink-02"><strong>一键完成</strong></span></p><p><span class="color-pink-02"><strong><span class="color-default">备用账号<span class="color-pink-02">创建完成</span>，打开网页客户端，用<span class="color-pink-02">刚刚设置的账号密码</span>登入，去管理界面也看到<span class="color-pink-02">所有权限</span>都有了！</span><br></strong></span></p><p><span class="color-pink-02"></span></p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/32f4dcfb1aa56caf4ab956863bf261520ce25eb7.png@44w_454h.webp" width="22" height="227" data-size="670" data-index="16" src="//static.hdslb.com/images/transparent.gif" style="width: 22px; height: 227px;"><figcaption class="caption">从 头 勾 到 底</figcaption></figure><p><span class="color-pink-02"></span><br></p><figure class="img-box"><img class="cut-off-4 loaded" src="//i0.hdslb.com/bfs/article/db75225feabec8d8b64ee7d3c7165cd639554cbc.png"></figure><figure class="img-box"><img class="cut-off-5 loaded" src="//i0.hdslb.com/bfs/article/4adb9255ada5b97061e610b682b8636764fe50ed.png"></figure><h1><span class="color-pink-02">下载测试版</span></h1><p><span class="color-gray-01">你都看到这里了，不如点个推荐和收藏吧！</span></p><figure class="img-box"><img class="cut-off-2 loaded" src="//i0.hdslb.com/bfs/article/4aa545dccf7de8d4a93c2b2b8e3265ac0a26d216.png"></figure><p><br></p><p><span class="color-default">由于最新的release版还放在学校实验室电脑里，现在在手里的只有<strong>去年十一月的发布版</strong>，感兴趣的可以先<strong>下载看看</strong>，<strong>收藏本专栏</strong>，等<strong>二月底开学</strong>我会上传<strong>最新版的项目文件及其release版本</strong>。<span class="color-default font-size-12">P.S.有没有人会熟练使用github，教教</span><br></span></p><p><span class="color-pink-02"><br></span></p><p><span class="color-blue-02"><strong>百度</strong></span><span class="color-gray-01">云盘</span><br></p><p><strong><span class="color-blue-02">Baidu</span><span class="color-gray-01">YunDisk</span></strong></p><p><span class="font-size-12"><strong>https://pan.baidu.com/s/1G0NqRGis4GmUYn12L_lj9g 提取码：BCBP</strong></span></p><figure class="img-box"><img data-src="//i0.hdslb.com/bfs/article/0bf7d0b85971d3c9c8daa21bf174e8399b1d6174.png@244w_254h.webp" width="122" height="127" data-size="9716" data-index="17" src="//static.hdslb.com/images/transparent.gif" style="width: 122px; height: 127px;"><figcaption class="caption">百度云下载</figcaption></figure><figure class="img-box"><img class="cut-off-4 loaded" src="//i0.hdslb.com/bfs/article/db75225feabec8d8b64ee7d3c7165cd639554cbc.png"></figure><p style="text-align: right;"><span class="color-pink-02"><strong>米花</strong></span></p></div>
