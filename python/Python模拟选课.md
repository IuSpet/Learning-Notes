# Python模拟选课

### 准备工具

- Python 3

- selenium

- 浏览器驱动

### 理论知识

##### 打开网页

```python
opt = webdriver.ChromeOptions()
driver = webdriver.Chrome(options=opt)
driver.get(url)
```

##### 找到元素

###### id定位

```python
"""
参数
"""
driver.find_element_by_id(id)

driver.find_elements_by_id(id)
```

name定位

```
driver.find_element_by_name(name)
driver.find_elements_by_name(name)
```

