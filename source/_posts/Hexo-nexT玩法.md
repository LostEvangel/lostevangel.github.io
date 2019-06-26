---
title: Hexo nexT的一些玩法（转载+总结）
date: 2017-11-22 15:50:14
tags: [Hexo]
categories: Hexo
photos:
  - "https://github.com/CherryKeinz/cherrykeinz.github.io/blob/master/images/cover/1.jpg?raw=true"
description: Hexo真是太棒了

---

# 基础篇

## 1. 常用命令

```
hexo new "HelloWorld" #新建文章
hexo new page "pageName" #新建页面
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署
hexo d #部署 #可与hexo g合并为 hexo d -g
```


## 2. 文章中插入图片

对于hexo，有两种方式： 
使用本地路径：在hexo/source目录下新建一个img文件夹，将图片放入该文件夹下，插入图片时链接即为/img/图片名称。 
使用微博图床，地址 http://weibotuchuang.sinaapp.com/ ，将图片拖入区域中，会生成图片的URL，这就是链接地址。
## 3. 添加分页、标签页面

新建
命令：

`hexo new page tags # tags可替换成其他`

输入命令后，在`/source`下会新生成一个新的文件夹tags，在该文件夹下会有一个index.md文件。
在上步新生成的`myBlog/source/tags/index.md`中添加`type: "tags"`，`index.md`文件内容如下：
设置具体文章的tags
当要为某一篇文章添加标签，只需在`/source/_post`目录下的具体文章的tags中添加标签即可，如：

`tags: [hexo,next]`

## 4. 引入第三方服务 

参考 : http://theme-next.iissnan.com/third-party-services.html#swfitype

 ### 1. 加入分享功能

在主题配置文件中，`jiathis: true`

 ### 2.  加入评论功能

  待续

 ### 3.  加入站点内容搜索功能

  1) 安装 hexo-generator-searchdb，在站点的根目录下执行以下命令：

`$ npm install hexo-generator-searchdb --save`

 2) 编辑 站点配置文件，新增以下内容到任意位置：

```
 search:
    path: search.xml
    field: post
    format: html
    limit: 10000
```
 3) 编辑 主题配置文件，启用本地搜索功能：

```
local_search:
enable: true
```

 4. 数据统计

##  5. 取消“文章目录”对标题的自动编号

修改主题配置文件那里的`toc:number`为`false`


# 个性化 ([参考](http://blog.csdn.net/qq_33699981/article/details/72716951))

## 1. 修改文章内链接文本样式（待考证）

修改文件 `themes\next\source\css\_common\components\post\post.styl`，在末尾添加如下css样式：

```css
// 文章内链接文本样式
.post-body p a{
  color: #0593d3;
  border-bottom: none;
  border-bottom: 1px solid #0593d3;
  &:hover {
    color: #fc6423;
    border-bottom: none;
    border-bottom: 1px solid #fc6423;
  }
}
```

## 2. 修改文章底部的那个带#号的标签
修改模板`/themes/next/layout/_macro/post.swig`，搜索 `rel="tag">#`，将 # 换成
/` <i class="fa fa-tag"></i> `/

## 3. 在每篇文章末尾统一添加“本文结束”标记
在路径` \themes\next\layout\_macro` 中新建 `passage-end-tag.swig` 文件,并添加以下内容：

```
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>
```

接着打开`\themes\next\layout\_macro\post.swig`文件，在post-body 之后， post-footer 之前添加如下画红色部分代码（post-footer之前两个DIV）：
![blog1.png](/image/blog1.png)

## 4. 修改网页底部的桃心
还是打开`themes/next/layout/_partials/footer.swig`，找到： 

```html
<span class="with-love">
    <i class="fa fa-heart"></i>
</span>
```

然后还是在[图标库](http://fontawesome.io/icons/)中找到你自己喜欢的图标，然后修改画红线的部分就可以了。
## 5. 添加顶部加载条
打开`/themes/next/layout/_partials/head.swig`文件，修改为 :

```html
<meta charset="UTF-8"/>
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"/>
<script src="//cdn.bootcss.com/pace/1.0.2/pace.min.js"></script>
<link href="//cdn.bootcss.com/pace/1.0.2/themes/pink/pace-theme-flash.css" rel="stylesheet">
<meta name="theme-color" content="{{ theme.android_chrome_color }}">
```

参考资料： 
http://www.jianshu.com/p/c23902f93558
http://blog.csdn.net/qq_33699981/article/details/72716951
http://playingfingers.com/2016/03/26/build-a-blog/
https://segmentfault.com/a/1190000003946969




## 5.添加网页音乐播放器功能

### Download Aplayer 

点击访问Aplayer源码：[GitHub Aplayer](https://github.com/MoePlayer/APlayer)。下载到本地，解压后将`dist`文件夹复制到`themes\next\source`文件夹下。

### 修改music.js

新建`themes\next\source\dist\music.js`文件，添加内容：

```
const ap = new APlayer({
    container: document.getElementById('aplayer'),
    fixed: true,
    autoplay: false,
    audio: [
	{
        name: "canon in d",
        artist: 'Brian Crain',
        url: 'http://pd2tflnys.bkt.clouddn.com/Brian%20Crain%20-%20canon%20in%20d.mp3',
        cover: 'http://p1.music.126.net/QGb9Vtyw7qHS00uEvPfM6g==/843325418547559.jpg?param=130y130',
      },
	  {
        name: "Apologize",
        artist: 'Martin Ermen',
        url: 'http://pd2tflnys.bkt.clouddn.com/Martin%20Ermen%20-%20Apologize.mp3',
        cover: 'http://p1.music.126.net/-_6mcI4VV5IKaiwhUAytbg==/1791104441647901.jpg?param=130y130',
      },
	  {
        name: "River Flows in You",
        artist: 'Yiruma',
        url: 'http://pd2tflnys.bkt.clouddn.com/Yiruma%20-%20River%20Flows%20in%20You.flac',
        cover: 'http://p1.music.126.net/8ZRSyI0ZN_4ah8uzsNd1mA==/2324367581169008.jpg?param=130y130',
      },
      {
        name: '惊蛰',
        artist: '音阙诗听/王梓钰',
        url: 'http://www.ytmp3.cn/down/48755.mp3',
        cover: 'http://p1.music.126.net/5MmXpaP9r88tNzExPGMI8Q==/109951163370350985.jpg?param=130y130',
      }
    ]
});
```

源码中对应的参数解释，这边都有： [Aplayer 中文文档](https://aplayer.js.org/#/zh-Hans/)

`audio`对应的便是音频文件，所以音乐播放器需要播放的音乐是需要自己进行相关信息（如歌曲链接、歌词、封面等）的配置。这里放一个mp3音乐外链网站：<http://up.mcyt.net/> ，搜索对应的音乐，然后复制`url`和右击封面图片链接粘贴到对应的位置上就行了。

注：由于该外链网站没有歌词链接，我这边没有进行配置，所以播放器在播放音乐时点击歌词是没有显示的。

### _layout.swig

打开`themes\next\layout\_layout.swig`文件，将

```
copy<link rel="stylesheet" href="/dist/APlayer.min.css">
<div id="aplayer"></div>
<script type="text/javascript" src="/dist/APlayer.min.js"></script>
<script type="text/javascript" src="/dist/music.js"></script>
```



添加到`<body itemscope ...>`后面就行，即在`<body></body>`里面。

重新生成，访问页面，就能看到左下角的音乐播放器了。


## 6. 搞怪网页标题

本章节参考 <http://yearito.cn/posts/hexo-advanced-settings.html>

```
themes\next\layout\_custom\custom.swig
```

```
{# 搞怪网页标题 #}
{% if theme.title_trick.enable %}
  <script>
    var OriginTitile = document.title;
    var titleTime;
    document.addEventListener('visibilitychange', function() {
      if (document.hidden) {
        document.title = '{{ theme.title_trick.leave }}' + OriginTitile;
        clearTimeout(titleTime);
      } else {
        document.title = '{{ theme.title_trick.enter }}' + OriginTitile;
        titleTime = setTimeout(function() {
          document.title = OriginTitile;
        }, 2000);
      }
    });
  </script>
{% endif %}
```

```
themes\next\layout\_layout.swig
```

```
      ...
      {% include '_third-party/exturl.swig' %}
      {% include '_third-party/bookmark.swig' %}
      {% include '_third-party/copy-code.swig' %}

+     {% include '_custom/custom.swig' %}
    </body>
  </html>
```

在主题配置文件中添加以下代码：

```
# a trick on website title
title_trick:
  enable: true
  leave: "啊咧?真由氏的怀表停了呢~"
  enter: "这一切都是命运石之门的选择！"

```

## 7. 豆瓣阅读 / 电影 / 游戏

```
 npm install hexo-douban --save
```

核心配置文件`_config.yml`：

```
douban:
  user:  # 个人豆瓣ID
  builtin: false  #是否每次hexo d -g时都自动hexo douban
  book:
    title: 'This is my book title'
    quote: 'This is my book quote'
  movie:
    title: 'This is my movie title'
    quote: 'This is my movie quote'
  game:
    title: 'This is my game title'
    quote: 'This is my game quote'
  timeout: 10000
```

在主题配置文件`themes\next\_config.yml`中：

```
menu:
    home: / || home
    tags: /tags/ || tags
    categories: /categories/ || th
    archives: /archives/ || tasks
+   books: /books/ || book  
+   movies: /movies/ || video-camera  
+   games: /games/ || gamepad
```

在语言包中新增菜单中文`themes\next\language\zh_CN.yml`：

```
  menu:
    home: 首页
    archives: 归档
    categories: 分类
    tags: 标签
+   movies: 电影
+   books: 读书
+   games: 游戏
```


