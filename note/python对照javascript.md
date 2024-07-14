



# pyExecJS模块

pyexecjs 可以帮助我们运行js代码的第三方模块

使用场景

* 没有第三方js
* 主要是原生态js内容的 js



前提: 安装 nodejs

然后

```python
# pip install pyexecjs
import execjs

# print(execjs.get().name)

# eval 执行一个js函数
# ret = execjs.eval("""
#     (function(){
#         return 1
#     })()
# """)


# print(ret)

# js脚本
js = """
function fn(a, b){
    return a + b
}
"""

# 绑定js脚本
a = execjs.compile(js)
# 执行js的 fn函数 , 其实a 就类似于 js的 window  fn 就是 window.fn(10, 20)
resu = a.call("fn", 10, 20)

print(resu)
```









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