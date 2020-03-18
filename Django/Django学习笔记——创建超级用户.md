# Django学习笔记——创建超级用户

创建超级用户，可以使用Django自带的管理系统，管理网站数据。

在控制台中输入指令

```
py manage.py createsuperuser
```

同样也可以在 `Tools` 菜单中选择 `Run manage.py Task..` 输入指令

之后根据控制台的提示依次输入用户名、邮箱、密码、确认密码，以上过程无误后，就创建好了超级用户

![image-20200115201702360](C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200115201702360.png)

启动程序后，输入URL `/admin/` 就可以进入网站管理界面，输入刚刚创建好的用户名和密码，就可以进入

![image-20200115201852090](C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200115201852090.png)

sudo apt-get install zlib1g-dev libbz2-dev libssl-dev libncurses5-dev libsqlite3-dev libreadline-dev tk-dev libgdbm-dev libdb-dev libpcap-dev xz-utils libexpat1-dev liblzma-dev libffi-dev libc6-dev