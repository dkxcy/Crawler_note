# AES加密

基础版

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

def aes_encrypt(text_string):
    
    KEY = ""
	IV = ""
	
    aes = AES.encrypt(
    	key=KEY.encode("utf-8"),
        mode=AES.MODE_CBC,
        iv=IV.encode("utf-8")
    )
    
    raw = pad(text_string.encode("utf-8"), 16)
    
    return aes.encrypt(raw)

text = "要加密的文本"

result = aes_encrypt(text)
print(result)
```

变换版本

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
import binascii

def aes_encrypt(text_string):
    
    KEY = "afdf" # 拿到的key不是常规的16个字节
	IV = "aadf" # 拿到的IV不是常规的16个字节
    
    KEY = binascii.a2b_hex(KEY) # 转换成16个字节
    IV = binascii.a2b_hex(KEY) # 转换成16个字节  此时是ascii码的字节
    
	
    aes = AES.encrypt(
    	key=KEY.encode("utf-8"),
        mode=AES.MODE_CBC,
        iv=IV.encode("utf-8")
    )
    
    raw = pad(text_string.encode("utf-8"), 16)
    
    return aes.encrypt(raw)

text = "要加密的文本"

result = aes_encrypt(text)
print(result)
```







# 网站分析方法

## 打开网页

<p style="color: red">tips:</p>

> 注意: 要用无痕模式打开网页避免cookie记录造成的影响
>
> 无痕模式下, 内部算法会从新计算一遍





## 分析网络请求

https://btrace.yangshipin.cn/kvcollect?BossId=2865&c_timestamp=lyhsowur_zngs0udz3tk

如上面的请求:

​	通过不同的页面, 发现

​	 BossId  是固定值

​	c_timestamp = 是变化的值

​		

将参数通过访问不同的页面, 排除固定值



### 分析: kvcollect请求

#### 关键字搜索法 

找请求的 c_timestamp 出自哪里

chrome 点击 search 搜索 c_timestamp

​							查找那些代码有这个参数

**注意**: 在 js的脚本中找, 而不是 url请求中找



在这个js中招  c_timestamp 关键字, find会列出有几个符合的



然后对比 这几个 c_timestamp 关键字的, 看看这几个生成方式是否一致, 看看是否有 关键算法, 

通过找同类项, 找到核心算法, 节省时间, 

如果没有找到核心算法, 那只能往上找



#### 分析请求体

看看请求体数据的来源

通过不同的页面对比请求体 ,排除固定项

**Pwd**: 1513256975 是固定的

**fvid**: w000051h2wu 是固定的, 页面请求的id

**c_guid**: lyhsgef2_alw66rytm4t ,这个可能是, 之前的请求拿到的, 也可能是算法生成的

**vurl**: 视频的随机的地址, 是不是请求拿到的, 还是算法生成的

怎么判断是否是算法生成,  方法: 找之前的请求,看看请求的返回值是否有这个参数, 如果没有就是算法生成的





**分析vurl 视频的链接请求**

```
vurl  https://mp4playcloud-cdn.ysp.cctv.cn/w000051h2wu.swqn10004.mp4?vkey=637B5AB17E9D68681C92A9354019EB37BF5F54E35B9235AE64A6A8088C7978C8C72259D6CC383DF1FF8D31B40A4190F2EF9A3F1432156D8D91AACBC08D6E07FC185D213BBA8F27AA88ED692BDF1AFC280135A945E94C3BE09BEA4CF2E5717BB8057DFCC66947E4630B6EBFFD1AD672B6&app_id=519748109&guid=lyhsgef2_alw66rytm4t&ysign=06ec0ca44879064174ff4b2871ba8cad&ytime=1720734576&ytype=1

参数 分析参数的组成,是否是固定, 
	非固定的参数是怎么来的 方法: 向上找请求的返回值, 没有就是算法生成的
vkey
app_id
guid
ysign
ytime
ytype
```







## 央视频 算法逆向

目标: 刷视频播放量

1. 分析添加视频播放量的请求 kvcollect?
2. 分析参数的组成, 
   1. 排除固定值和页面id的对应值,    
   2. 非固定值的生成, 方法: 来自之前的请求, 不是就是来自算法生成的







流程: 

刷视频播放量, 参数

1. pid
2. guid
3. vurl   又来自playinfo? 请求
   1. playinfo 的参数
   2. vkey 的参数组成
      1. guid 
      2. flowid
      3. _md
      4. ckey 解, ckey的算法
   3. guid
   4. 视频资源地址
4. 





