# Django学习——常用模型增删查改API

[TOC]

​		根据文档记录几个模型的API，后面根据使用情况再补充

​		以学生-课程数据模型为例子：

- 学生（学生ID，姓名，性别，年级）

- 课程（课程ID，课程名，老师）

- 选课（选课ID，学生ID，课程ID，成绩，课程属性）

  ​	使用Django的模型层定义这些数据端

```python
class Course(models.Model):
    name = models.CharField(max_length=20)
    teacher = models.CharField(max_length=20)


class Student(models.Model):
    name = models.CharField(max_length=20)
    gender = models.CharField(max_length=10)
    grade = models.IntegerField(default=2020)
    courses = models.ManyToManyField(Course, through='SC')


class SC(models.Model):
    student = models.ForeignKey(Student, models.CASCADE)
    course = models.ForeignKey(Course, models.CASCADE)
    score = models.IntegerField(default=0)
    attribute = models.CharField(max_length=10)
```

​		执行命令

```
>py manager.py makemigrations
>py manager.py migrate
```

​		成功执行后，相应的SQL语句已经被执行，在数据库中自动创建了以下表

**Student表**

id、name、gender、grade

**Course表**

id、name、teacher

**SC表**

id、score、attribute、course_id、student_id

### 增加对象

#### 实例化对象和save()方法

​		在模型层定义的类对应着一个数据库的表项，每个类的一个实例就是相应的表中的一个元组

​		所以对应数据库的**增**操作就是创建一个模型类的对象

```python
s1 = Student(name='aaa',gender='male',grade=2019)
s1.save()
```

​		save()方法必须调用，调用save()后Django才操纵数据库执行操作

​		执行了以上代码后，查看数据库

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200117205658832.png" alt="image-20200117205658832" style="zoom:80%;" />

​		数据库中保存了刚创建的学生对象

#### 实例化对象和create()方法

​		使用create()方法可以一步完成实例化和存储数据到数据库的操作

```python
c1 = Course.objects.create(name='sc',teacher='zzz')
```

​		执行了以上代码后，查看数据库

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200117210713508.png" alt="image-20200117210713508" style="zoom:80%;" />

#### 创建带有外码的对象

```python
s1 = Student.objects.get(pk=1)
c1 = Course.objects.get(pk=1)
sc1 = SC(student=s1,course=c1,score=88,attribute='elective')
sc1.save()
```

​		执行以上代码后，查看数据库

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200117212535972.png" alt="image-20200117212535972" style="zoom:80%;" />

#### 使用add()方法添加关联对象

​		在Student模型中，添加了字段courses用于和Course模型的多对多关联，在数据库中，建立了sc表来表示这一关联，使用add()方法就可以添加关联对象，而对应的在sc表中会创建一个元组

```python
s2 = Student.objects.get(pk=2)
c1 = Course.objects.get(pk=1)
s2.courses.add(c1)
```

​		执行以上代码后，会在sc表中创建一个新的元组

​		但是，在定义模型时，使用了中间模型来为学生和课程关系添加额外的属性：成绩与课程属性，在上面的调用中，这两个属性会设置为默认值，可以用 `through_defaults` 参数指定这些属性的值

```python
s2 = Student.objects.get(pk=2)
c2 = Course.objects.get(pk=2)
s2.courses.add(c2,through_defaults={'score':55,'attribute':'elective'})
```

​		执行以上代码后，会在sc表中创建一个新的元组，且带成绩与课程属性，查看数据库

<img src="C:\Users\10378\AppData\Roaming\Typora\typora-user-images\image-20200118154051477.png" alt="image-20200118154051477" style="zoom:80%;" />

### 检索对象

​		在前面已经使用了查询操作，可以看出是用 `模型类.objects.get()` 来进行查询，其中 `模型类.objects` 是一个[控制器](https://docs.djangoproject.com/zh-hans/2.2/topics/db/managers/#django.db.models.Manager)

​		Django中只能用模型类调用api查询，不能用类的实例调用

#### 检索全部对象

控制器的all()方法		

```python
>>> Student.objects.all()
# <QuerySet [<Student: Student object (1)>, <Student: Student object (2)>]>
```

重写 `__str__` 方法可以改变显示

#### 通过过滤器检索指定对象

​		实质改变了SQL语句的**WHERE**子句的内容

```python
filter(**kwargs)
# 返回一个新的 QuerySet，包含的对象满足给定查询参数。
-----------------------------------------------------------------------------
exclude(**kwargs)
# 返回一个新的 QuerySet，包含的对象 不 满足给定查询参数。
-----------------------------------------------------------------------------
get(**kwargs)
# 返回匹配查询条件的对象
# 有多个结果抛出  MultipleObjectsReturned 异常
# 没有结果抛出  DoesNotExist 异常
```

​		[更多的QuerySet 方法](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#queryset-api)

```python
>>> Student.objects.filter(grade=2022)
# <QuerySet [<Student: Student object (2)>]>
>>> Student.objects.exclude(grade=2020)
# <QuerySet [<Student: Student object (1)>, <Student: Student object (2)>]>
```

​		QuerySet也能调用以上方法，所以可以多个条件串联起来进行链式查询

```python
>>> Student.objects.exclude(grade=2020).filter(grade=2022).get(grade=2022)
# <Student: Student object (2)>
# get()方法返回的是模型类的对象
```

​		QuerySet对象可以储存起来，不会被其他操作影响到，可以之后继续调用方法查询

​		QuerySet对象只有在被计算时进行数据库操作

​		可以使用Python的切片语法改变QuerySet的长度，**不能用负数索引**

```python
>>> Student.objects.all()[0:5]
# <QuerySet [<Student: Student object (1)>, <Student: Student object (2)>]>
```

​		虽然返回的还是QuerySet对象，但是切片操作后，不能继续过滤和排序

```python
>>> Student.objects.all()[0:5].get(grade=2022)
# Traceback (most recent call last):
# …………
# AssertionError: Cannot filter a query once a slice has been taken.
```

#### filter()和exclude()方法的参数

​		除了用 `=` 设置过滤条件，还有其他过滤条件可以设置，在属性后面加双下划线 `__` 就可以使用

##### exact

​		进行精确的匹配

```python
Student.objects.filter(grade__exact=2022)
# 等价于
Student.objects.filter(grade=2022)
```

##### iexact

​		不分大小写的匹配

```python
Course.objects.filter(name__iexact='cs')
```

##### contains

​		大小写敏感的包含

```python
Student.objects.filter(name__contains='a')
```

​		大致对应的WHERE子句

```sql
WHERE name LIKE '%a%';
```

##### icontains

​		大小写不敏感的包含

```python
Student.objects.filter(name__icontains='a')
```

##### in

​		在给定的可迭代对象内

```
Student.objects.filter(grade__in=[2019,2020,2021])
Student.objects.filter(name__in='abc')
```

​		大致对应的WHERE子句

```sql
WHERE grade IN (2019,2020,2021);
WHERE name IN ('a','b','c');
```

##### gt

​		**g**reater **t**han，比给的数字大

```python
Student.objects.filter(grade__gt=2018)
```

​		大致对应的WHERE子句

```sql
WHERE grade > 2018;
```

##### gte

​		**g**reater **t**han or **e**qual to，大于等于给的数字

```python
Student.objects.filter(grade__gte=2018)
```

##### lt

​		**l**ess **t**han，小于

##### lte

​		**l**ess **t**han or **e**qual to，小于等于

##### startswith

​		大小写敏感的开头匹配

```python
Student.objects.filter(name__startwith='李')
```

##### istartswith

​		大小写不敏感的开头匹配

##### endswiths

​		大小写不敏感的结尾匹配

##### iendswith

​		大小写不敏感的结尾匹配

##### range 

​		范围

```python
SC.objects.filter(score__range=(80,89))
```

​		大致对应的WHERE子句

```sql
WHERE score BETWEEN 80 AND 89;
```

#### 跨关系查询

​		假如想查询老师是 ’zzz‘ 的学生，Django提供了跨关系查询，同样也是用双下划线 `__` 连接

```python
Student.objects.filter(courses__teacher='zzz')
```

​		也可以反向关联，比如查询有2022级学生上的课程，**反向关联要把模型类名小写**

```python
Course.objects.filter(student__grade=2022)
```

#### 主键查询快捷方式

​		Django提供主键（**p**rimary **k**ey）查询快捷方式

```
Student.objects.get(pk=1)
# 返回主键是1的Student对象
```

### 删除对象

#### delete()方法

​		使用该方法删除对象，返回被删除的对象数量和一个包含了每个被删除对象类型的数量的字典。

​		模型类的对象和QuerySet都能调用此方法，但是管理器不能调用该函数

```
s1=Student.objects.get(pk=1)
s1.delete()

SC.objects.filter(score__lt=60).delete()
```

### 修改对象

#### 修改多个对象，update()方法

​		使用该方法更新对象，对指定字段执行SQL的更新操作，并返回匹配的行数

```python
SC.objects.filter(socre__lt=60).update(score=60)
```

​		**更新不能跨关系执行**

#### 修改单个对象

​		单个模型类的实例不能调用update()方法

​		但是可以获取到该对象后修改相应的值，再用save()方法保存

```python
sc = SC.objects.get(pk=2)
sc.score = 100
sc.save()
```