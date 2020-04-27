# Flask单元测试post请求发送数据

&emsp;&emsp;在用unittest库对flaks app进行单元测试时，有时需要模拟post请求发送数据

&emsp;&emsp;使用post请求发送的数据，一般有两种格式，一种是表单数据，一种是json数据，两种数据在flask的后端获取的操作也不同，对应的在测试时，模拟方式也不同

### 表单数据

&emsp;&emsp;后端拿数据

```python
request.form[key]
request.form.get(key)
```

&emsp;&emsp;测试时构造

```python
class RestUnitTest(TestCase):
    def setUp(self) -> None:
        app.testing = True
        self.client = app.test_client()

    def test_UserLogin(self, mock_getpwd):
        rv = self.client.post('/users/root/login', data={"password": "123456"})
        self.assertEqual(rv.status_code, 200)
```

&emsp;&emsp;表达数据在构造时就是一个字典，以各种方法构造出一个字典作为data的值传入即可

### json数据

&emsp;&emsp;后端拿数据

```python
request.json[key]
request.json.get(key)
```

&emsp;&emsp;测试时构造

```python
class RestUnitTest(TestCase):
    def setUp(self) -> None:
        app.testing = True
        self.client = app.test_client()

    def test_UserLogin(self, mock_getpwd):
        rv = self.client.post('/users/root/login', content_type='application/json', data='{"password": "123456"}')
        self.assertEqual(rv.status_code, 200)
```

&emsp;&emsp;json数据是一个字符串，所以data的值要传入一个字符串，同时因为json不是默认格式，必须加入 `content_type='application/json'` 说明数据格式

### 参考资料

[测试 Flask 应用](http://docs.jinkan.org/docs/flask/testing.html)

[POST请求发送的表单数据和json数据的区别及python代码实现](https://blog.csdn.net/chouzhou9701/article/details/104579939)