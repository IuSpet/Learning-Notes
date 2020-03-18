# Django学习笔记——创建项目

[toc]

### 创建Django项目

在PyCharm里选择创建新目录，选择Django项目，选择Python版本和Django的项目名称，点击Create即可创建

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\1578834615492.png" alt="1578834615492" style="zoom: 80%;" />

​		创建完成后，运行该项目，进入控制台提示的URL，出现以下页面表示项目创建成功

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\1578835734498.png" alt="1578835734498" style="zoom:80%;" />



### Django项目结构

​		使用Django创建好项目后，就会有以下目录结构

![1579000885100](C:\Users\10378\AppData\Roaming\Typora\typora-user-images\1579000885100.png)

​		图中例子中，`mysite` 是项目根目录， `polls` 是创建项目时创建的第一个应用（一个项目可以包括多个应用，可以理解为一个功能模块），创建其他应用，可以顶部菜单选择 `Tools > Run manage.py task Task` 之后在下面出来的窗口中输入

```
startapp [应用名称]
```

​		尝试直接在控制台输入命令 `py manage.py startapp [应用名称]`  虽然也能添加文件结构，但是运行会报错（不知道是不是因为我的默认django版本和这个anaconda开的虚拟版本不一样）

#### 项目根目录

##### settings.py：

​		Django 项目的配置文件。

##### urls.py：

​		 Django 项目的 URL 声明，就像你网站的“目录”，在这个文件中可以指定处理对应URL的函数。

##### wsgi.py：

​		作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。

##### manage.py：

​		 一个让你用各种方式管理 Django 项目的命令行工具。

#### 应用目录：

