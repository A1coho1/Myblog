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
summary: 自己对Matery主题的修改
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

## 3.修改banner部分（未完成）

## 4.将featureImages替换为图床加快访问速度（未完成）
