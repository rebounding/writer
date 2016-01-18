---
title: 使用 Hexo 和 GitHub 搭建 blog
date: 2016-01-17 21:08:28
categories:
- system
tags:
- hexo
- github
---
使用免费的 GitHub Page 搭建个人 blog 已经变得非常流行，而主流的静态 blog 系统主要就是两大类：
- [Jekyll](https://jekyllrb.com/)
- [Hexo](https://hexo.io/zh-cn/)

Jekyll 相对 Hexo 而言，更复杂一些，个人尝试了 Hexo 之后，觉得还是更喜欢 Hexo 一些，毕竟搭建 blog 的目的是写文章，反复折腾系统就走偏了。网络上关于如何搭建基于 Hexo 和 GitHub 的 blog 的文章比比皆是，这里就不再造轮子了，记录一些我搭建过程中的重点。关于主题，我选择了 NexT，这是一个非常流行的主题，在 GitHub 上收获了 2000+ star。感谢 Hexo 和 NexT 的作者，提供给我们这么好的工具。

<!-- more -->

## 参考资料
- [Hexo 框架官方文档](https://hexo.io/zh-cn/docs/)
- [NexT 主题官方文档](http://theme-next.iissnan.com/)

仔细阅读官方文档，足以搭建起 blog 了，不明白的地方可再 Google 一下。搞清楚目录结构，分清楚两个用到的 _config.yml 文件，根目录下是站点配置文件，theme 目录下是主题配置文件。

## Hexo 常用命令
``` bash
$ hexo new post <title> # 新建文章
$ hexo clean # 清除db和public
$ hexo g # 生成public
$ hexo s # 启动本地服务
$ hexo d # deploy文章
```
hexo new 命令完整应是
``` bash
$ hexo new [layout] <title>
```
Hexo 有三种默认布局：post、page 和 draft，它们分别对应不同的路径，建立 blog 之初，需要使用 page 去设置不同的目录，比如：tags。而建立完成之后，更多的就是使用 post，写文章即可，draft 功能可以不用。
Hexo 需要将 [Markdown](https://www.zybuluo.com/mdeditor?url=https%3A%2F%2Fwww.zybuluo.com%2Fstatic%2Feditor%2Fmd-help.markdown) 形式的文章生成静态 html，然后发布到 GitHub 上去，生成的文件放在 public 目录下。
Hexo 提供了本地调试 server，使用 [-p port] 可以指定端口，默认是 4000。
Hexo 支持直接将文章发布到 GitHub 上，只需要在站点配置文件中写上：
``` bash
deploy:
  type: git
  repo: https://github.com/XXXX/XXXX.github.com.git
```
这样执行 hexo deploy 命令就可以一键发布了。然而，网络上看到大家普遍遇到的问题是，执行命令后没反应，既没有报错，也没有成功发布。一些情况是格式问题，type 和 repo 之后需要有空格。但是我仔细确认了格式，仍然是不行。最后只能绕道，将 public 中的文件手工发布到 GitHub 上，倒是可以写脚本山寨一键发布功能。

所以，最终我的写作流就变成了先生成新文章，然后编辑，本地调试，最后自己提交 public 的内容到 GitHub 上。

## 资源文件夹
[资源文件夹](https://hexo.io/zh-cn/docs/asset-folders.html) 是我认为有必要单独说一下的点，默认它是关闭的。资源文件夹提供了更好的资源组织，打开后生成文章时会生成对应的资源目录，图片等资源就可以放在这个资源目录下，使用时也很方便。开启资源文件夹只需要修改站点配置文件，如下：
``` bash
post_asset_folder: true
```
使用方法参考官方文档即可。

## 支持评论
基于 GitHub Page 的 blog 系统都是使用第三方评论插件，常用的有国内的多说、友言，国外的DISQUS。NexT 主题对第三方评论系统做了很好的支持，使用起来相当方便。默认支持多说和DISQUS，多说优先级更高。看一下 themes/next/layout/_partials/comments.swig 文件：
``` javascript
{% if page.comments %}
  <div class="comments" id="comments">
    {% if (theme.duoshuo and theme.duoshuo.shortname) or theme.duoshuo_shortname %}
      <div class="ds-thread" data-thread-key="{{ page.path }}" 
           data-title="{{ page.title }}" data-url="{{ page.permalink }}">
      </div>
    {% elseif theme.disqus_shortname %}
      <div id="disqus_thread">
        <noscript>
          Please enable JavaScript to view the 
          <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a>
        </noscript>
      </div>
    {% endif %}
  </div>
{% endif %}
```
可以看到只需要填入 shortname 即可，多说生效时就不会继续走判断 DISQUS 的代码分支。
而 NexT 的主题配置文件中有如下代码：
``` bash
# Duoshuo ShortName           
#duoshuo_shortname:
```
只需打开注释，把自己注册的 duoshuo_shortname 填上即可。这时再生成页面，就可以在对应的 html 页面中找到相关的评论系统代码了。

## 数据统计
Hexo 对数据统计的支持也很好，预置了 Baidu 和 Google 的统计。只需要将注册好的 ID 填入：
``` bash
# Baidu Analytics ID
baidu_analytics:
```
或者
``` bash
# Google Analytics
google_analytics:
```
即可。

## 站内搜索
Hexo 支持使用 Swiftype 作为站内搜索。Swiftype 是一个第三方站内搜索服务，注册后，指定网站地址，Swiftype 的爬虫就会遍历我们的站点，构建索引，然后提供在线的检索服务。Swiftype 提供了 30 天免费使用，到期后可以使用功能缩减的免费版，作为个人 blog 也够用了，完整功能需要付费。不得不说这个思路真是不错，是一种商业模式。
与其他第三方插件的使用方式类似，只要将注册好的 Key 填入对应位置即可：
``` bash
# Swiftype Search API Key     
swiftype_key:
```

## 小技巧
### 文章折叠
首页一般为了提供更好的导航，每篇文章都显示的是标题和摘要，使用如下标签：
``` xml
<!-- more -->
```
截取摘要。
如果想要使得文章展开后，直接跳转到截取摘要的位置，需要确认主题配置文件：
``` bash
# Automatically scroll page to section which is under <!-- more --> mark.
scroll_to_more: true
```
默认是开启了的。

至此，可以看到一个 blog 系统应有的功能基本上都满足了，如此的简便，低廉，使用 Hexo 的同时也可以顺便了解一下 Node.js，也是蛮不错的。
