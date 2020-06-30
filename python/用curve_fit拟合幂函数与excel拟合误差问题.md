# 用curve_fit拟合幂函数与excel拟合误差问题

&emsp;&emsp;使用`curve_fit`可以对自定义的函数进行拟合

&emsp;&emsp;拟合幂函数时，具体代码为

```python
from scipy.optimize import curve_fit

def pow_func(_x, _a, _b):
    return _a * pow(_x, _b)

x_axis = [...]
y_axis = [...]
popt, _ = curve_fit(linear_func, x_axis, y_axis)
a, b = popt
```

&emsp;&emsp;但是同样的数据，使用excel中的幂函数拟合后，两边的a、b不同

&emsp;&emsp;查询资料发现，在excel中，拟合幂函数时，会先左右取对数然后进行线性拟合

&emsp;&emsp;`curve_fit`可以拟合多种不同的函数，底层显然不会有这样的操作

&emsp;&emsp;为了和excel中的结果保持一致，需要更改curve_fit拟合的函数，左右取对数后再拟合

```python
from scipy.optimize import curve_fit
from numpy import log

def linear_func(_x, _a, _b):
    return log(_a) + _b * _x
  
x_axis = [...]
y_axis = [...]
popt, _ = curve_fit(linear_func, log(x_axis), log(y_axis))
a, b = popt
```

&emsp;&emsp;这样拟合后，a、b值与excel结果相同

