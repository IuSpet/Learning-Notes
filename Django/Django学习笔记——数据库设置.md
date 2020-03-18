# Django学习笔记——数据库设置

[toc]

### 修改使用的数据库为MySQL

​		Django 默认使用 SQLite 作为数据库。如果想使用其他数据库，需要在项目目录的 `settings.py` 更改 `DATABASES` 的内容。

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\1579004294958.png" alt="1579004294958" style="zoom:80%;" />

​		至少添加以上属性，如果有其他要去，可以按照[官方文档](https://docs.djangoproject.com/zh-hans/2.2/ref/settings/#std:setting-DATABASES)添加其他属性。

​		此外还要下载相应的驱动程序，这不是必须的依赖包，用conda安装Django时并没有安装。Django官方推荐的MySQL驱动是 `mysqlclient` ，使用以下命令安装

```
conda install mysqlclient
```

如果没有使用 anaconda 或者 miniconda ，使用pip安装

```
pip install mysqlclient
```

### 创建模型

​		在各个应用的 `models.py` 中可以定义该应用使用的数据结构

​		//todo：模型层的语法简述

​		例如官网文档的投票应用例子，创建模型的代码为

```python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('data published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

```

### 激活模型

​		在 `models.py` 中创建了模型后，还没有转换为对应的SQL语句执行

​		使用以下代码为模型的改变生成迁移文件

```
py manage.py makemigrations
```

​		使用以下代码进行数据库迁移，这一步会自动对数据库的所有更改执行SQL语句

```
py manage.py migrate
```

