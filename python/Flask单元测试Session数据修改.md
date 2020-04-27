# Flask单元测试Session数据修改

&emsp;&emsp;在测试某些模块时，需要session数据，而测试时启动的客户端与用浏览器访问不同，不会保存Cookies，需要其他方法去模拟session

### 访问Session

&emsp;&emsp;利用 with 语句创建一个上下文环境访问session

```python
@patch('model.User.getPassWord')
def test_UserLogin200(self, mock_getpwd):
    mock_getpwd.return_value = '123456'
    with app.test_client() as c:
        rv = c.post('/users/root/login', content_type='application/json', data='{"password": "123456"}')
        self.assertEqual(rv.status_code, 200)
        assert session['username'] == 'root'
```

&emsp;&emsp;使用 with 语句块启动一个测试客户端发出请求，在 with 块中，可以使用这次请求的上下文信息，如 `session`，`request` 等，利用这个方法就可以测试session中是否写入自己期望的值

### 修改session

&emsp;&emsp;在前面的方法中，只能去访问已有的session，不能创建session，要修改session，需要`session_transaction`()

```python
def test_getTitle200(self, mock_get_title):
    with app.test_client() as c:
        with c.session_transaction() as sess:
            sess['username'] = 'root'
        rv = c.get('/users/root/title')
        self.assertEqual(rv.status_code, 200)
        assert b'test title' in rv.data
```

&emsp;&emsp;这里要**特别注意缩进**，在`with c.session_transaction() as sess:` 的块中只修改session，剩下的请求工作要回到上一级的块中，因为只有这个块结束，这些修改才会写入session。官网文档中这没写清楚，我全部写到一个块里后抛出的异常比代码还长=-=

&emsp;&emsp;这里测试了一个获取标题的请求，在该请求中，会用session检查用户是否登陆，如果不修改session直接测试，返回401，加入session修改后，返回200，测试成功

### 参考资料

[测试 Flask 应用](http://docs.jinkan.org/docs/flask/testing.html#sessions)