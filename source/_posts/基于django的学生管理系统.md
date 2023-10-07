---
title: 基于django的学生管理系统
top: false
cover: false
toc: true
mathjax: false
img: 'https://pic.imgdb.cn/item/6520fa16c458853aef28c6a8.png'
summary: 实现了增删查改以及数据统计功能
tags:
  - django
  - sql
categories: web
abbrlink: 49949
date: 2023-10-04 15:59:58
updated: 2023-10-04 15:59:58
author:
coverImg:
password:
---
## 1. 项目简介及预览

### 1.1 简介

项目地址以及下载：[Github](https://github.com/A1coho1/stu_score_management) (需要科学上网)

基于mysql+django的学生成绩管理系统，主要涉及学生信息管理、课程信息管理、成绩信息管理以及统计分析四个模块。

其中学生、课程、成绩三大模块均实现了增删改查功能，统计分析部分使用 [echarts](https://echarts.apache.org/zh/index.html) 进行可视化。

### 1.2 预览

根据课程名或课程代码查找对应课程，并显示相应统计信息。

![](https://pic.imgdb.cn/item/6520fa16c458853aef28c6a8.png)

根据学号或姓名查找对应学生，并显示相应统计信息。

![](https://pic.imgdb.cn/item/6520fa16c458853aef28c689.png)

## 2. 涉及的主要技术栈

- 前端: Bootstrap+Jquery+AJAX
- 后端: Django

## 3. 项目部署

- Clone项目或下载zip到本地
- 进入项目根目录并安装依赖库:

```shell
pip install -r requirements.txt
```

- 进入/stu_score_management/settings.py修改数据库相关设置:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'db_sc',           # 要使用的数据库名称，建议新建一个数据库
        'USER': 'root',            # 用户名，默认为root
        'PASSWORD': '******',      # 你的密码
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

- 创建你刚刚所填写的数据库，以'db_sc'为例:

  ```shell
  create database db_sc;
  ```
- 执行数据库迁移命令:

  ```shell
  python manage.py makemigrations
  python manage.py migrate
  ```
- 启动项目:

  ```shell
  python manage.py runserver
  ```
