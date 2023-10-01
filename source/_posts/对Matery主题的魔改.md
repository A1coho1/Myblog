---
title: 对Matery主题的魔改
top: false
cover: true
toc: true
mathjax: false
date: 2023-10-01 03:14:20
author:
img: https://ts1.cn.mm.bing.net/th/id/R-C.15156de8ecdaca0e208f755572b5217b?rik=SCL92Je0wYHVXg&riu=http%3a%2f%2fheibanbai.com.cn%2fimg%2fhexo.png&ehk=VywdH%2f1oS1u8iXWWEm11T%2bm0ntJ9pczrqS%2bktzxU3Mo%3d&risl=&pid=ImgRaw&r=0
coverImg:
password:
summary: 主要涉及到css, ejs模版
tags:
 - 魔改
 - Hexo
 - Matery 
categories: Hexo
---
## 1.文章页面调整

### 1.1调整页面逻辑，让顶部显示区域更大

将./source/css/matery.css中的.post-cover从40vh改为70vh

```css
.post-cover {
    height: 70vh !important; /*从原本的40改为了70*/
}
```

### 1.2取消post-cover自动生成的background-image

在./layout/_partial/post-cover.ejs中原本的逻辑是：

- Front-matter中填写了img属性则以其作为文章特征图并生成顶部的background-image
- Front-matter中没有填写img属性则根据文章标题的 `hashcode`的值取余，然后选取对应的 `featureImages`作为文章特征图并生成顶部的background-image

现在删去其生成的background-image以达到顶部无背景图片的效果

```css
<div class="bg-cover pd-header post-cover"> <!--此处删去了 style="background-image: url('<%- featureimg %>')" -->
    <div class="container" style="right: 0px;left: 0px;">
        <div class="row">
            <div class="col s12 m12 l12">
                <div class="brand">
                    <h1 class="description center-align post-title"><%= page.title %></h1>
                </div>
            </div>
        </div>
    </div>
</div>
```

### 1.3修正移动端图片拉伸问题

将./layout/_partial/background.ejs中的background-size改为cover

```css
body{
    background-image: url(<%- theme.background.url %>);
    background-repeat:no-repeat;
    background-size:cover; <!--原本为 background-size: 100% 100%; -->
    background-attachment:fixed;
}
```

## 2.修改全局背景为随机图片

### 2.1新增自己的功能模块

在_config.yml中新增 `backgroundImages`，建议使用**图床**的方式

```yml
backgroundImages:
- ...
- ...
- ...
```

### 2.2修改background.ejs

修改./layout/_partial/backgrounds.ejs中的内容如下：

```ejs
<% 
    var num = Math.floor(Math.random() * theme.backgroundImages.length)
    var URL = url_for(theme.backgroundImages[num])

    if (theme.background.enable) { %>
        <style>
            body{
                background-image: url(<%- URL %>);
                background-repeat:no-repeat;
                background-size:cover;
                background-attachment:fixed;
            }
        </style>
    <% } 
%>
```

### 2.3现在的网页配置

**禁用**了全局背景，只在已经发表的文章中显示全局背景，其他地方均只显示banner图片。

在./layout/_partial/post-cover.ejs文件中增加以下内容：

```ejs
<% 
    var num = Math.floor(Math.random() * theme.backgroundImages.length)
    var URL = url_for(theme.backgroundImages[num])

     %>
        <style>
            body{
                background-image: url(<%- URL %>);
                background-repeat:no-repeat;
                background-size:cover;
                background-attachment:fixed;
            }
        </style>
    <% 
%>
```

并在_config.yml修改全局背景为 `false`

## 3.修改banner部分

修改./layout/_partial/bg-cover-content.ejs

```ejs
<% if (theme.banner.enable) { %>
<script>
    // 每天切换 banner 图.  Switch banner image every day.
    var bannerUrl = "<%- theme.jsDelivr.url %><%- url_for('/medias/banner/') %>" + new Date().getDay() + '.jpg';
    $('.bg-cover').css('background-image', 'url(' + bannerUrl + ')');
</script>
<script>
    <% if (theme.banner.enable && theme.banner.random) { %>
      var bannerUrl = "<%- theme.jsDelivr.url %><%- url_for('/medias/featureimages/') %>" + Math.floor(Math.random() * <%- theme.featureImages.length %>) + '.jpg';
    <% } else { %>
      // 每天切换 banner 图.  Switch banner image every day.
      var bannerUrl = "<%- theme.jsDelivr.url %><%- url_for('/medias/banner/') %>" + new Date().getDay() + '.jpg';
    <% } %>
    $('.bg-cover.about-cover').css('background-image', 'url(' + bannerUrl + ')');

</script>
<% } else { %>
<script>
    $('.bg-cover').css('background-image', 'url(<%- theme.jsDelivr.url %><%- url_for('/medias/banner/0.jpg') %>)');
</script>
<% } %>
```

修改后如下:

```ejs
<% 
var num = Math.floor(Math.random() * theme.backgroundImages.length)
var URL = url_for(theme.backgroundImages[num])

%>
<style>
    .bg-cover {
        background-image: url(<%- URL %>);
    }
</style>
<%

%>
```

## 4.文章标题增加打字机效果

修改./layout/_partial/post-cover.ejs

定位到标题的位置

```ejs
<h1 class="description center-align post-title"><%= page.title %></h1>
```

修改后如下：

```ejs
<h1 class="description center-align post-title">
    <span id="post-title"></span>
    <% if (theme.post.enable) { %>
    <script src="<%- theme.jsDelivr.url %><%- url_for(theme.libs.js.typed) %>"></script>
    <script>
        var typedObj = new Typed("#post-title", {
            strings:  ['<%= page.title %>'],
            startDelay: <%= theme.post.startDelay %>,
            typeSpeed: <%= theme.post.typeSpeed %>,
            loop: <%= theme.post.loop %>,
            backSpeed: <%= theme.post.backSpeed %>,
            showCursor: <%= theme.post.showCursor %>
        });
    </script>
    <% } %>
</h1>
```

最后在 **_config.yml** 中进行配置:

```yaml
# 文章标题
post:
  enable: true
  loop: false # 是否循环
  showCursor: true # 是否显示光标
  startDelay: 500 # 开始延迟
  typeSpeed: 100 # 打字速度
  backSpeed: 50 # 删除速度
```

## 5.将featureImages替换为图床加快访问速度（未完成）
