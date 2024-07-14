### 请求百度翻译

<p style="color: red">application/json 一定要用 requests.post(url, json=data) # 发送的是json格式
<p style="color: orange">
    而不是 request.post(url, data=data)


```
import requests
from requests_html import HTMLSession

url = "https://fanyi.baidu.com/ait/text/translate"

data = {
"corpusIds": []
,"reference": ""
,"domain": "common"
,"from": "zh"
,"to": "en"
,"qcSettings": ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11"]
,"needPhonetic": False
,"milliTimestamp": 1719925184508
,"query": "中文"
}

headers = {
    "Accept": "text/event-stream"
    ,"Accept-Language": "zh-CN,zh;q=0.9"
    ,"Connection": "keep-alive"
    ,"Accept-Encoding": "gzip, deflate, br, zstd"
    ,"Content-Type": "application/json" ##重点重点
    ,"Referer": "https://fanyi.baidu.com/mtpe-individual/multimodal?query=%E4%B8%AD%E6%96%87&lang=zh2en&ext_channel=Aldtype"
    ,"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36"
    ,"Acs-Token": "去网页复制",
    "Cookie": "去网页复制"}

# 创建一个HTMLSession对象
session = HTMLSession()

# 设置POST请求的URL和数据

# 发送POST请求    注意 application/json 一定要用 json发送请求
resp = session.post(url, json=data, headers=headers, stream=True)

# 确保响应的Content-Type是'text/event-stream'
if resp.headers.get('Content-Type') == 'text/event-stream; charset=utf-8':


    # 处理EventStream事件
    for event in resp.iter_lines():
        if event:
            # 这里可以根据实际的事件数据进行相应的处理
            print(event)
        else:
            # 心跳数据或其他空数据
            print('Heartbeat')
else:
    print(resp.headers)
    print('Error: Incorrect Content-Type')

# 关闭session
session.close()
```









开始找源码 获取

milliTimestamp 和 query的关系

```
找到源码位置
1. 从发起请求的地方找到 
2. 往前找上一级
3. 找到发送请求的地方, 有 数据的地方
4. 找数据是这么出现的, 数据溯源,从下往上找
5. 找到数据编码的函数, 溯源		(注意有时候, 你不知道某个变量哪里来的可以去找页面源代码)
		# 某个函数用来不知道哪里来到可能在 exports里面, 或闭包里面
		   你可以去 打个断点, 去查这个函数在哪里
		    进到这个函数里面

```

百度翻译搞不定, (搞定了, 但是用不到pyExecJS因为 所有参数都是明文)





原则 

```
找到请求的加密算法, 将加密算法都复制出来


js1 = "
	开头放 变量, 避免后续报错
	复制整个算法在这里
	
	里面如果有未知的变量, 则将变量的算法搞出来如下面,
	
"

js2 = "
	开头放变量 
	复制整个算法
	
	得到结果
"

ret = execjs.eval(js2)

js = "
	{ret} //把算好的数据 插入js里面
"
jscode = execjs.compile(js)

jscode.call("fn", *args) # *args函数接收的参数



```

<p style="white-space: pre-wrap;background: #eee"> <i style="color: orange">总结:</i>
     缺什么补什么, 能复制就复制, 没有变量就制造变量,他的算法复制出来, 变量为固定值就写固定值
    算法
    	找变量  (找不到就找html)
    	找函数
    给程序加断点, 溯源慢慢的往回查
</p>

