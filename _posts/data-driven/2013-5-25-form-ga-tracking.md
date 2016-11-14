---
layout: post
title: 通过表格填单的Google Analytics跟踪代码添加，来看待网站表格转化
description:
heading: 通过表格填单的Google Analytics跟踪代码添加，来看待网站表格转化
category: data-driven
publish: true
published: true
datePublished: 2013-5-25T00:00:00.000Z
dateModified:
date: 2013-5-25
---

对于网上的填单信息页这种的数据跟踪和网站分析，是一件很有意思的事情。通常，我们需要跟踪用户来到网站后成功提交联系我们的的转化数，多步骤的信息填单的流失跟踪，繁琐的步骤会让人不耐烦的。这里，跟大家分享一个WordPress的Gravity Forms怎么添加GA的代码和Goal设置介绍。

联系我们的页面提交跟踪

通过重定向到一个特定的URL，然后将这个URL设定到GA的一个目标里面，当做一个目标URL即可。这是最简单快捷的一种。选择第二个的Page也是可以的。只要目标页面有GA的跟踪代码就可以了。

<amp-img src="/assets/img/post/form-tracking-1.png" width="499" height="271"></amp-img>

第二种的话是Text的形式，也就是说填写完Contact Us的信息之后，成功提交，URL不变化，页面变成文本的形式：谢谢，您的信息已提交。我们将尽快联系您。如下图。
<amp-img src="/assets/img/post/form-tracking-2.png" width="499" height="271"></amp-img>

那么，要使得这个被GA触发，只要触发一段script就可以了，可以用trackevent也可以用trackPageview，但用event会好点。最后，到GA的管理员设置一个目标，用匹配event的就可以了。这里有一个注意事项，整段script不要有额外的空行，否则会触发不出来。

表格填单的填错信息次数

大家肯定都经历过，当填写一些网上表格的时候的，当邮箱没有写@的时候，提交按钮后一定会提示你，请输入正确的邮箱地址。所以对于Gravity Forms的步骤型表单填写出错的时候，会提示一段There was a problem with your submission. Errors have been highlighted below. 然后要实现当出现这一段文字的时候，就发送给GA一次的trackevent就可以了。找到对应的PHP文件之后，在这段文件之后加入类似这样的代码：

> <script type=”text/javascript”>jQuery(document).ready(function($) {_gaq.push([‘_trackEvent’, ‘error’, ‘form-$form_id’,  ‘page-$current_page’, 1]); });</script>

注意事项是这段代码加入函数里面的话要注意一些语法问题，同时利用$current_page也可以知道是哪一个步骤出错信息最多了。当然这是一些比较容易实施JS代码的例子，对于中大型的站点，肯定有专业工程师团队来帮你做更高级的代码来实现更多的跟踪需求，强力推荐大家看博主网站优化在中国的这篇《网上表格的优化-从基础数据找机会》，肯定可以让大家认识到网上填单跟踪的重要性。

另外，还可以对表单的Button点击次数，以及传入填写的信息进行传值，比如传入年龄，性别等维度。这个就要因表单的类型而异了。然后对于电子商务表单的填写，最感悟的是就是如果没有跟踪每一个步骤的流失率，那么就根本不能判断流程设计的好与坏了。特别是错误填错的分析。

总的来说，对于网站分析来说，表单分析是很有趣的一块。百度统计里面也有一款跟踪报表事件跟踪，可以利用唯一ID来跟踪一些上传类的按钮跟踪。大家可以关注下。

注：所有文章及图片版权归网站数据吧（WebData8.com）所有。转载请注明出处“<a href="/">Webdata8网站分析</a>”。
