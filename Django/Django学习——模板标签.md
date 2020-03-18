# Django学习——模板标签

[TOC]

​		根据文档，记录总结一些常用的模板标签

## 标签

### autoescape

​		控制自动转义行为，带有 `on` 和 `off` 参数进行控制

​		在Django中，转义是自动生效的

​		假如有如下代码

```django
#视图
context["content"] = "<p>title</p>"

# 模板
{{ content }}
```

​		该变量在模板中输出时会自动转义，变成 `&lt;p&gt;title&lt;/p&gt;`

​		最终在浏览器中的显示依然是 `<p>title</p>`  

​		将模板代码改为

```django
{% autoescape off%}
    {{ content }}
{% endautoescape %}
```

​		改变量在模板中输出时不会转义，依然是 `<p>title</p>` 

​		最终在浏览器中的显示变为 `title` 

### comment

```django
{% comment [可选的说明] %}
    <代码块>
{% endcomment %}
```

​		忽视掉两个标签中的一切，即注释的作用，可以在第一个标签中写一些说明

​		用法就是当想删去某块代码，可以用该标签将代码包裹，在第一个标签内写下原因

​		该标签不能嵌套使用

### csrf_token

```django
<form method="post">
{% csrf_token %}
```

​		CSRF：**C**ross **S**ite **R**equest **F**orgery

​		该标签是Django用来抵御跨站点伪造请求的安全保护手段之一

​		在用POST请求访问站内的URL表单下面，加上该标签

​		访问外部URL的表单下不加

​		如果不想使用该标签，可以删除 `settings.py` 内 `MIDDLEWARE` 内的 `'django.middleware.csrf.CsrfViewMiddleware'`

### cycle

​		循环访问该标签，会不断迭代使用标签内的参数，到结尾后回到第一个参数

## 过滤器

### add

### safe