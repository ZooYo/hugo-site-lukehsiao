---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "【Django 筆記】Migrate搞得我頭好痛"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2020-05-20T11:27:41+08:00
lastmod: 2020-05-20T11:27:41+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
# Migrate Cause Error!

* makemigrations => 產生對Database操作的檔案 e.g. 0001_initial.py
* migrate => 利用上述檔案對db進行操作 e.g. 建表、欄位
* showmigrations => 檢視migrate狀態

### multi-databases 注意！

當你要migrate給預設外的db時，要加上 --database 指定db，否則會造成建表失敗等問題
> python manage.py migrate <app_name> --database <database_name>

### 參考資料
1. [How to Reset Migrations](https://simpleisbetterthancomplex.com/tutorial/2016/07/26/how-to-reset-migrations.html)
2. [Django migrations with multiple databases](https://stackoverflow.com/questions/35609509/django-migrations-with-multiple-databases)