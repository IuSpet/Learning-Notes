# Flask单元测试Mock简单使用

&emsp;&emsp;在单元测试中，需要测试一个单独模块的功能运行情况，这使得我们不希望它被其它模块的功能所影响

&emsp;&emsp;举例来说，想测试一个get请求返回的数据是否正确，但是在处理get请求的方法中，数据来源于其他模块函数。在测试中，我们希望测试get请求时，只测试处理该请求的方法，而不关心获取数据的方法，这时就需要用mock来模拟获得数据的方法

&emsp;&emsp;**这里只介绍最简单的一种模拟，即模拟一个方法**

### 要测试的函数

&emsp;&emsp;具体情况不同，写的代码不是完整代码

```
def get():
	...
	data = model.User.getUserName()
	...
	return ...
```

### 单元测试

&emsp;&emsp;从`unittest`包中导入`mock`模块

```python
import unittest
from unittest import TestCase
from unittest.mock import patch
from app import app

class UnitTest(TestCase):
    def setUp(self) -> None:
        # 这个app是你的flask应用，也要导入
        app.testing = True
        # 启动测试用的客户端发送请求
        self.client = app.test_client()
    
    # 用装饰器添加要模拟的方法名
    @patch('model.User.getUserName')
    # 在参数中添加模拟前面方法的Mock对象，(实际是MagicMock对象，Mock的子类)
    def test_UserLogin200(self, mock_getpwd):
        # 设置该模拟方法的返回值
        mock_getpwd.return_value = 'admin'
        # 用客户端发送用要测试模块处理的请求
        rv = self.client.get('/username')
        # 测试返回数据是否正确
        assert b'admin' in rv.data 
```

&emsp;&emsp;在这个测试中，用 Mock 模拟了 `model.User.getUserName` 方法，在测试get方法时，不再依赖于 `model.User.getUserName` 的结果