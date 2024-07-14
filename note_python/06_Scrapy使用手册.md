# startr_requests()  

startr_requests 源码中负责开始阶段的发起请求

```python
# 重写    直接就可以使用 避免反扒机制导致无法访问
def start_requests():
    
    # 从网页拿到cookies 拿到 直接复制浏览器的cookie
    cookies = "cookies文本"
    
    dic = {}
    
    for item in cookies.split("; ")
    	k, v = item.split("=")
        dic[k] = v.strip()
    
    
    yield scrapy.Request(
        url=self.start_urls[0],
    	headers={},
        cookies=dic, #cookies 是一个字典
    )
```





发送post请求  方案1

```python
def xiaoSpider(scrapy.spider):
    
    def start_requests():
    # 添加下面的几个
        url = "https://地址"
        username="12312312"
        password="naer"
        yield scrapy.Request(
        	url="https://sf.com/login", # 登录地址
            method="post"
            body=f"username={username}&password={password}"
        )
    
    def parse(self, resp, *args):
        
        #得到登录信息 直接yield出去
        
        # 下面开始请求首页
        yield  scrapy.Request(
        	url=self.start_urls[0], # 登录地址
            method="get"
           	callback=self.parse_detail
        )
        
	def parse_detail(self, resp, *args):
        # 得到了起始页的信息
        print(resp)




```



post 方式2   formRequest() 更方便

```python
def start_requests():
    # 添加下面的几个
        url = "https://地址"
        username="12312312"
        password="naer"
        yield scrapy.formRequest(
        	url="https://sf.com/login", # 登录地址
            method="post"
            formdata={
                "username": username,
                "password": password
            }
        )
```







from 和 form的区别

from  来自拐杖扎着的肉去麦当劳烤肉

form  表格填写拐杖或麦当劳



# 中间件

robots 协议的关闭

```python
# 在settings.py 里面
ROBOTSTXT_OBEY = False # 关闭
```





DownloadMiddleware() 重点

```
# 创建创建中间件
class midDownloaderMiddleware:

	@classmethod
	def from_crawler() # 负责middle的流程管理
	
	# 发起请求之前的中间件
	def process_request(self, request, spider):
    	"""
    		return: 返回值的区别 
    		1. None 跳过这个请求
    		2.request    请求被拦截,后续中间不再执行, 请求将重新交给引擎, 引擎重新扔给调度器
    		3. response  后续中间件将不再执行, 将响应信息交给引擎, 引擎交给spider 
    	"""
    	
    	
    	
    	return None
    
    
    # 下载器响应之后, 交给引擎的步骤
    process_reponse(self, request, response, spider):
    	
    	
    	
    	return response
    
    


```



```python
# settings 上挂载中间件
DOWNLOADER_MIDDLEWARES = {
    "mid.middlewares.MidDownloaderMiddleware": 300  #挂载中间件 
}
```





### 案例: 动态User-Agent

```
# settings 里面设置一个变量
USER_AGENT_LIST = [
	"agent的列表"
]
```

middleware使用

```python
# 导入user-agent列表
from settings import USER_AGENT_LIST
# 导入随机的 函数包
from random import choice

# 创建创建中间件
class midDownloaderMiddleware:

	@classmethod
	def from_crawler()
	
	# 发起请求之前的中间件
	def process_request(self, request, spider):
    	# 随机拿到一个
        ua = choice(USER_AGENT_LIST)
        request.headers["User-Agent"] = ua
        
        # 不用return 或 直接None
        return None
```



### 案例: 使用代理









隧道代理

```python
# 导入http处理成 base64的包
from w3lib.http import basic_auth_header

# 创建创建中间件
class midDownloaderMiddleware:

	@classmethod
	def from_crawler()
	
	# 请求时使用 隧道代理
	def process_request(self, request, spider):
    	
        proxy = "代理服务器的ip"
        request.meta["proxy"] = "http://%s" % proxy
        # 这里需要转成base64发送用户名密码避免乱码
        request.headers["Proxy-Authorization"] = basic_auth_header("12用户名", "safd密码") 
        
        request.headers["Connection"] = 'close'
        
        return None
```





### 案例: selenium 中间件

```python
# 我此时要替换掉原来的 download 改用 selenium

# settings 里面
DOWNLOADER_MIDDLEWARES = {
   "boss.middlewares.BossDownload_Middleware" : 99 # 直接提升我自己的 中间件到 99   因为默认的中间件最大是100
}

# 注意上面这样子会让所有请求都走selenium
```



```python
# 我们需要重新写一个请求类,  下面协议有SeleniumRequest
from selenium.webdirver import Chrome, ChromeOptions
from scrapy.http.response.html import HtmlReponse
	
class BossDownloaderMiddleware:

	@classmethod # 控制流程的
	def from_crawler()

	def process_request(self, request, spider):
    	
        # 判断是否是 SeleniumRequest 发送的请求 
        if isinstance(request, SeleniumRequest):
            # 下面走selenium
            self.web.get(request.url)
            
        	page_source = self.web.page_source
            # 返回页面源代码
            return HtmlReponse(
            	url=request.url,
                status=200,
                request=request
                body=self.web.page_source,
                encoding="utf-8"
            )
        	
        
        
        return None
    
    # 当spider 启动的时候 创建浏览
    def spider_opened(self, spider):
        
        web = Chrome(option=opt)
        self.web = web
        
```





我自己设置一个请求,   为了后续请求做判断使用selenium

```python
# request.py 文件 自己创建


class SeleniumRequest(Request)
	pass  
```

spider  发起请求的使用 通过这个 请求 发送请求 

```python
class spider():
    
    
    def start_request():
        
        
        yield SeleniumRequest(
        	url= "",
            method="get"
        )
```



如果你要关闭浏览器, 你就要在from_crawler() 注册一下

```python
@classmethod
def from_crawler(cls, crawler):
    # This method is used by Scrapy to create your spiders.
    s = cls()
    							函数              执行时机   signals里面有很多东西去看看 
    crawler.signals.connect(s.spider_opened, signal=signals.spider_opened)
    return s
```











### 案例: selenium 模拟登录拿到cookies

在 downloader_middleware 里面

```python
# 我们需要重新写一个请求类,  下面协议有SeleniumRequest
from selenium.webdirver import Chrome, ChromeOptions
from scrapy.http.response.html import HtmlReponse
	
class BossDownloaderMiddleware:

	@classmethod # 控制流程的
	def from_crawler()

	def process_request(self, request, spider):
    	# 判断一下, 没有cookie 就绑定cookie进去
        if not request.cookies:
            request.cookies = self.cookie
        
        
        return None
    
    # 当spider启动的时候 创建浏览
    def spider_opened(self, spider):
        
        web = Chrome(option=opt)
        self.web = web
        
        web.get("http://登录页面")
        web.find_element() # 用户名
        web.find_element() # 密码
        img = web.find_element().screenshot_as_base64
        verify_code = self.base64_api("username", "password", img, 3) # 校验验证码
        web.find_element().keys_send(verify_code)# 输入验证码
        web.find_element().click() #登录
        
        time.sleep(3)
        cookies =  {}
        for item in web.get_cookies(): # 拿到selenium 的 cookies, 处理一下
        	cookies[item["name"]] = cookies[item["value"]]
        # 上面三句代码可以改用
        # cookies = {item["name"] = item["value"] for item in web.get_cookies()}
        
        self.cookie = cookies
        
        
```

