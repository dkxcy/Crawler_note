# 关于时间模块

### 

```python
from datetime import datetime

# js: new Date()    获取当前时间戳
datetime.now()  
# js date["getTime"]()  获取当前时间毫秒值
datetime.now().timestamp() * 1000 # timestamp 当前时间秒值


```







# 进制转换

转36进制

```python
# 实现 toString(36) 功能
def decimal_to_36(decimal): # 传入一个10进制数字

    digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    result = ""
    while decimal > 0:
        remainder = decimal % 36
        result = digits[remainder] + result
        decimal = decimal // 36
    # 得到一个36进制的 字符串
    return result

```



# 随机数







# 加密模块