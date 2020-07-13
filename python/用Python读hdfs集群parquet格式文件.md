# 用Python读hdfs集群parquet格式文件

### 使用模块

- Python2.7

- subprocess

- io.BytesIO

- parquet-python 1.3.1

其中`parquet`包不是python内置，需要用`pip` 安装

### 整体流程

1. 使用`subprocess`读hdfs中的parquet文件

2. 将字节流写入`io.BytesIO`对象中（要做这一步是因为`parquet`包目前只支持文件对象解析）

3. 使用`parquet`包的接口解析上一步得到的io对象

### 代码

```python
import parquet
import subprocess
from io import BytesIO

url='文件路径'
popen_args = ['hdfs', 'dfs', '-text', url]
p = subprocess.Popen(popen_args, stdout=subprocess.PIPE)
f = BytesIO()
for line in p.stdout:
    f.write(line)
p.wait()
if p.returncode == 0:
	for row in parquet.reader(f):
		print row
```

`parquet`包还提供一个`DictReader`的接口，使用也很简单

### 参考资料

[parquet-python](https://github.com/jcrobak/parquet-python)

