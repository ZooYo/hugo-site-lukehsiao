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

# 利用例子觀察指令對local及Database的變化
> ##### python manage.py startapp example
> - 檔案系統:
>     - example/migrations/\_\_init\_\_.py
> - Database.django_migrations(資料表): 無變化 

    python manage.py showmigrations:
    example
      (no migrations)

***
### 新增models

    class Book(models.Model):
      name = models.CharField(max_length=30)

> ##### python manage.py makemigrations example
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py 
> - Database.django_migrations(資料表): 無變化 

    python manage.py showmigrations:
    example
      [ ] 0001_initial

***
> ##### python manage.py migrate example
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py 
> - Database.django_migrations(表): 新增一個0001\_init的row

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499 

    python manage.py showmigrations: 
    example
      [X] 0001_initial

***
### 修改models

    class Book(models.Model):
      name = models.CharField(max_length=30)
      author_name = models.CharField(max_length=30, default='Unknown')

> ##### python manage.py makemigrations example
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py
>    - example/migrations/0002\_book\_author\_name.py
> - Database.django_migrations(資料表): 無變化

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499

    python manage.py showmigrations:
    example
      [X] 0001_initial
      [ ] 0002_book_author_name

***
> ##### python manage.py migrate example
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py
>    - example/migrations/0002\_book\_author\_name.py
> - Database.django_migrations(表): 新增一個0002\_book\_author\_name的row

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499 
24|example|0002_book_author_name|2020-05-25 09:19:11.180593

    python manage.py showmigrations: 
    example
      [X] 0001_initial
      [X] 0002_book_author_name

***
### migrate 回第一個點
> ##### python manage.py migrate example 0001
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py
>    - example/migrations/0002\_book\_author\_name.py
> - Database.django_migrations(表): ID為24的row消失了\!

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499 

    python manage.py showmigrations: 
    example
      [X] 0001_initial
      [ ] 0002_book_author_name

此時若開啟admin site的Book會看到
> OperationalError at /admin/example/book/ <br/>
> no such column: example_book.author_name

那是因為Database中已經沒有author_name的欄位，但django的model還有這個欄位，造成欄位不一致，此時可以選擇
  1. 將models.py中的欄位及0002\_book\_author\_name.py 刪除，回到0001\_initial.py的狀態
  2. migrate至 0002 修改後再次 makemigrations && migrate
***
## sqlmigrate
我們選擇1.回到0001\_initial.py的狀態
### 修改models

    class Book(models.Model):
      name = models.CharField(max_length=30)
      price = models.IntegerField(default=99)

> ##### python manage.py makemigrations example
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py
>    - example/migrations/0002\_book\_price.py
> - Database.django_migrations(資料表): 無變化

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499

    python manage.py showmigrations:
    example
      [X] 0001_initial
      [ ] 0002_book_price

此時我們可以使用sqlmigrate，觀察migrate會對database執行什麼指令

    python manage.py sqlmigrate example 0002
    BEGIN;
    --
    -- Add field price to book
    --
    CREATE TABLE "new__example_book" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "price" integer NOT NULL, "name" varchar(30) NOT NULL);
    INSERT INTO "new__example_book" ("id", "name", "price") SELECT "id", "name", 99 FROM "example_book";
    DROP TABLE "example_book";
    ALTER TABLE "new__example_book" RENAME TO "example_book";
    COMMIT;

嗯嗯，建新表 => 複製內容 => 丟掉舊表 => 將新表的名稱改為就表的名稱 => commit

***
## \-\-fake

> ##### python manage.py migrate example \-\-fake
> - 檔案系統:
>    - example/migrations/\_\_init\_\_.py
>    - example/migrations/0001\_initial.py
>    - example/migrations/0002\_book\_price.py
> - Database.django_migrations(資料表): 新增一個0002\_book\_price的row

id|app|name|applied 
---|---|---|---  
21|example|0001_initial|2020-05-25 09:01:07.499
26|example|0002_book_price|2020-05-25 10:02:57.057379


    python manage.py showmigrations:
    example
      [X] 0001_initial
      [X] 0002_book_price

目前為止看起來和migrate都一樣啊！ <br/>

此時若開啟admin site的Book會看到
> OperationalError at /admin/example/book/ <br/>
> no such column: example_book.price

原因是，\-\-fake會讓狀態在migrations之間移動，但不會真正的對DB做操作，也就是說當前的Database並沒有price欄位，那要如何回到原本的狀態呢？ <br/>
（此時並沒有辦法migrate，因為對migrations的狀態來說，他已經在0002了，因此降至0001再重新執行migrate）

    python manage.py migrate example 0001
    python manage.py migrate

*因此做任何操作前，最好先確認當前DB狀態與migrations版本（showmigrations）及狀態*

## multi-databases 注意！

當你要migrate給預設外的db時，要加上 --database 指定db，否則會造成建表失敗等問題
> python manage.py migrate <app_name> --database <database_name>

### 參考資料
1. [How to Reset Migrations](https://simpleisbetterthancomplex.com/tutorial/2016/07/26/how-to-reset-migrations.html)
2. [Django migrations with multiple databases](https://stackoverflow.com/questions/35609509/django-migrations-with-multiple-databases)