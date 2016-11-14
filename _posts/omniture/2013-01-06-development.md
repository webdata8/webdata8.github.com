---
layout: post
title: 如何和技术团队配合Omniture SiteCatalyst跟踪代码的实施
description:
heading: 如何和技术团队配合Omniture SiteCatalyst跟踪代码的实施
category: omniture
publish: true
published: true
datePublished: 2013-01-06T00:00:00.000Z
dateModified:
date: 2013-01-06
---

我们知道，许多网站分析工具都必须添加跟踪代码，有的是一段Javascript代码，有的也可以是img图片代码，但这一切都必须离不开工程师团队配合，而对于中大型网站来说，开展网站分析工具的跟踪代码布局往往是一件很复杂很漫长的事情。而对于众多的网站分析工具来说，Omniture的跟踪代码实施是需要小心翼翼的，如果一个环节出现问题，就可能导致数据跟踪错误。

当然，Omniture的客户服务会有实施顾问和工程师培训项目，有一些是需要昂贵的培训费用的。不过我想，如果公司不愿额外花钱，最好可以组织个小组，有开发人员，有测试人员，然后大家一起各施所长吧。期间还能找出Omniture的一些功能缺陷和Bug。本文就来说说和开发技术团队配合Omniture跟踪代码实施的一些经历。

1.分享Omniture官方开发技术手册给相关的工程师

我觉得在做Omniture SiteCatalyst跟踪代码布局之前最好要把Omniture官方的跟踪代码实施文档多看几遍，同时一定要分享给你的工程师团队，还可以挑选一些重点做成PPT来和工程师进行交流。当遇到开发问题的时候，与其人云亦云地到Google找答案，还倒不如拿出这个PDF文档仔细研究，相信没有什么材料比这些更权威了，它一定是最有说服力的。再者里面有相当多插件和变量介绍，如果实施跟踪代码的工程师能更深入的了解SiteCatalyst代码的设计，也有助于以后的跟踪代码开发和维护。比如需要避免的代码错误，实现的方式，还有就是考虑如何设计一套稳定可扩展的Omniture跟踪代码程序架构。你还可以将代码导入IDE的工程里面，一起和工程师来开展跟踪代码的代码修改和测试。这么一来，跟踪代码需求肯定会事半功倍，至少和工程师可以很好地配合和交流。

2.数据收集的流程，添加全局跟踪代码

首先，在Omniture的后台申请一个报表包之后都会对应一个跟踪代码，有两部分，需要让工程师用文件将他们维护起来，保证两部分都可以出现所有页面文件的公共底部。

第一部分

><script language=”JavaScript” type=”text/javascript” src=” /js/s_code_account.js”></script>

该文件是Javascript库文件，类似于GA或百度统计贴的那段公共代码，里面有很多函数还可以继续修改和升级，比如几年前是G版代码，现在可以升级到H版，这些实施顾问都会配合你的需求。

第二部分是

><script language=”JavaScript” type=”text/javascript”><!–s.pageName=””;s.eVar10=””;s.events=”,”;s.campaign=””;s.channel=””;var s_code=s.t();if(s_code)document.write(s_code)//–></script>

这用来填充SiteCatalyst的一些变量，发送给Omniture。比如有页面名称，变量，事件和促销活动等，最后通过call函数s.t()实现，这个s.t()相信大家就能联想到GA的那个什么的吧？哈哈。一般来说，第一部分的代码是公共的，也就是整站都会统一的，而第二部分的代码就会根据不同的页面而变化，根据不同的业务逻辑流程而改变了，因为有一个很显然的变量：s.pageName，对，没错，接下来就是如何友好架构每一组页面的名称了。

3.s.pageName的设计

如果没有对SiteCatalyst做特别的代码定制，只把默认的JS代码包添加到底部，那么能得到的数据适合其他分析工具差不多的。而对页面名称（s. pageName变量）的设计就是非常伤脑筋的事情。简单地说，s. pageName 非常类似于GA的虚拟页面功能，可以针对某一个或一组URL重写页面名称，和SEO的URL rewrite原理一样，好处是可以规律性看报表。

第一步就是和工程师配合，把全站的文件全部罗列出来，这里的文件不是是指具体的URL，一般来是说是影响某一些页面的文件，因为有一些页面是动态生成的，有的是通过ftl或者jsp文件生成的，有的更可以是写在action里面，有了这些文件，以后要想知道哪些页面没有加跟踪代码就容易了，但这点对大中型站点往往是困难的。

之后把整个站点的频道和层级，以及各块业务线全部通过组织架构图的方式画出来，这样就有一份站点的Sitemap了。简单以一个NBA官网站点为例，首页自然是第一层级，页面名称就可以为s. pageName =hompage，而每一只球队的主页可以是第二层级，比如湖人队主页s. pageName =Los Angeles Lakers，接下去自然是球员主页了，比如科比s. pageName= Los Angeles Lakers：Kobe，再比如联系我们和关于我们s. pageName就可以设置为contact us和about us，当然如果没有设置s. pageName，Omniture会自动读取URL，这样有可能对于一组杂乱无章的URL你就无法汇聚流量来分析了。最典型的是电子商务的报价列表页。当用户填入一些年龄性别城市等信息之后到达一些带有参数页面如https://www.website.com/product-1/quote.action?type=one&age=65，这个可以统一合并成一个s. pageName=product one : quote page，这种设置一般是针对很多参数为了合并成一张页面的情况，一般来说这组页面流量都很大，不然想做细分就没有办法了，除非在设置一些s.prop或者s.eVar参数再去过滤。附带说下，这样还有一个优点就是可以看整张报价页面的Click Map点击图。（注：把整站的Sitemap画出来是对未来做站点的成功事件跟踪，页面设计，SEO的一个重要步骤）

4.跟踪代码文件的设计

一般来说，实施SiteCatalyst站点的跟踪代码是一件长期的工作，一个美国的同事曾经花了1年多的时候才把为某些核心业务需求而设计跟踪代码发布成功，所以对于有技术实力的公司都会针对这些代码进行架构，保证可维护和可扩展性。比如针对变量用一个OmnitureVariable的文件来维护，这样以后其他工程师在使用的时候就可以清楚地调用或继承这些类文件。简单的来说， 可以分为这几步：

1）处理页面请求的时候，通过某一个文件根据配置信息给跟踪数据设初始值

2）当请求经过数拦截器的时候，拦截器会调用action类相应方式，添加或者重写赋予SitaCatalyst代码里的值

3）生成页面的时候，通过一些文件里的数据输出到页面代码中

4）浏览器执行页面代码的时候，通过js或non-js的方式把跟踪数据和变量值提交到Omniture服务器

不过，上面我请教工程师的时候得到的一些思路，对于更坚固的流程设计整套跟踪代码的架构和跟踪代码的功能开发，配合跟踪代码需求的工程师都会帮你解决。事实上，一直以来，进行SiteCatalyst跟踪代码的布局实施中，我认为开发人员扮演着举足轻重的角色。甚至在Adobe官网，还专门开设一个上面关于Omniture开发的参考资料社区，同时个人认为购买SiteCatalyst的公司都会有强大的技术支持的。

5.实施代码的Debug工具

<amp-img src="/assets/img/post/debug.png" width="573" height="359"></amp-img>

注：选取美国站点law.com作为例子

由于很多大中型站点的开发流程非常完善和严谨，有本地环境，测试站点环境以及正式站点的环境。因此工程师在做一个Omniture跟踪代码需求的时候需要在本地的站点先开发才会提交到测试站点。因此工程师需要在做完跟踪代码后在本地做一次调试，最典型的是Adobe官方的Debug工具。其中还是编辑修改功能，供开发人员调试。更多的工具，推荐大家看网站优化在中国博主的一篇博文《<a href="http://www.chinaweboptimization.com/2011/07/omniture-tools/" target="_blank">Omniture实施常用工具</a>》。

最后提一个优化跟踪代码的小例子，因站而异。 比如对一个电子商务站点产品列表的查看按钮的跟踪，我们会实施SiteCatalyst的link跟踪代码，故每个产品的点击按钮也许会加上这样的代码

><a onclick=”s.linkTrackVars=’products,events,eVarN’;s.linkTrackEvents=’eventN,eventM’; s.events=’eventN,eventM’;eVarN=’$Value$’;s.products=’$product line$;$plan name$;$order counts$;$price$’;s.tl(this,’o’,’$Button_PlanName$’); href=” “><img src=” ” ></a>

如果列表页有100个产品，就会出现100个这样代码段，但出于后期维护的有效性，建议可以单独创建一个函数来控制产品变量，比如OmnitureProductButtonClick(Plan ID)，这么一来当点击按钮触动a标签的onclick的OmnitureProductButtonClick函数，然后去读取写在函数里面的Plan ID这个参数，然后发送上面的含在$$中的并且关于这个产品ID的变量值，比如价格，产品线，自定义链接名称等。这样一来一大堆的代码就被缩写成OmnitureProductButtonClick（123）了，这样也利于以后其他类似功能的调用。

上面是在实施SiteCatalyst的过程中和工程师会考虑的一些点，同时我觉得一样适用Google Analytics或是百度统计等其他网站分析工具，对于复杂度来说关键还是看数据跟踪需求。总的来说Omniture的产品可以看做是一种BI工具，可以传入很多很多有意义的变量值进去，然后去使劲挖掘，这时候很需要工程师的配合。如果你有更多和工程师配合的注意事项，欢迎多分享和交流哦。

注：所有文章及图片版权归网站数据吧（WebData8.com）所有。转载请注明出处“<a href="/">Webdata8网站分析</a>”。
