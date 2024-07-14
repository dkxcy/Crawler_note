# 前置 

安装linux   

```
ifconfig // 查看 ip地址

# 登录管理员权限
su - 登录root
123456 
```

安装docker

```
yum install docker 

# 替换一下docker的源
vi /etc/docker/daemon.json # 文件路径
{
	"righstry-mirrors": ["https://registry.docker-cn.com/"]
}
# 按esc   :wqb保存并退出

# 运行docker
systemctl start docker
docker ps # 检查docker是否运行成功

systemctl restart docker 重启docker
systemctl top docker 停止docker
```





# splash 

安装splash 

```
docker pull scrapinghub/splash 

# 运行splash 
docker run -p 8050:8050 scrapinghub/splash

# 打开浏览器访问你的splash

http://linuxip:8050
```

splash 的 lua脚本

```lua
function main(splash, args):
	splash:go(args.url) # 访问地址
    splash:wait(15) # 等待时间
    return {
        html = splash.html(), #返回html源码
        png = splash.png(), # 返回页面截图
        har = splash.har() # 返回请求信息
    }
end
```



splash的 http-api接口

```
# render 接口拿到页面的源码
http://192.168.164.32:8050/rander.html?url=http://www.baidu.com

# png 接口拿到 页面截图
http://192.168.164.32:8050/rander.png?url=http://www.baidu.com

# har 接口 拿到请求加载过程, (页面所有的请求信息)
http://192.168.164.32:8050/rander.png?url=http://www.baidu.com

# json 拿到 json数据
http://192.168.164.32:8050/rander.json?url=http://www.baidu.com

# execute 执行lua脚本
http://192.168.164.32:8050/execute?lua_source=lua脚本

```

python实例 render 获取页面源码

```python
http://192.168.164.32:8050/rander.html?url=http://www.baidu.com

requests.get("http://192.168.164.32:8050/rander.html", 
				params={
					url: "http://www.baidu.com",
					wait: 15
				})
```





python实例 网易新闻      execute 执行lua脚本, 

```python
lua_source = """
function main(splash, args)
  assert(splash:go("%s")) -- 跳转的页面
  assert(splash:wait(0.5)) -- 等待
  -- 创建js函数
  get_display_btn = splash:jsfunc([[
    function (){
        return document.querySelector(".load_more_btn").style.display 
    }
  ]])
  
  
 
  while(true)
  do
    display = get_display_btn() -- 执行js, 获取返回值
    if(display == "none") -- 合适的时机跳出循环
    then
        break
    end
    
    splash:runjs("doucment.querySelector('.load_more_btn').scrollIntoViewIfNeeded()") -- 滚动页面
    splash:select(".load_more_btn").click() -- 点击元素
    splash:wait(1) -- 注意等待, 不等待可能会报错
  
  end
  
  return {
    html = splash:html(),
    png = splash:png(),
    har = splash:har(),
  }
end
"""

lua_text = lua_source % "http://www.baidu.com"

requests.get("https://linux:8050/execute", params: {
    "lua_source": lua_text  -- lua脚本
})


```



## scrapy-splash 包

lua_source

```
上面有 注意修改args.url 或者直接把http写死在 lua_source里面
```

安装运行 scrapy-splash

```python
pip install scrapy-splash


class SplSpider(scrapy.Spider):
    name = "spl"
    allowed_domains = ["163.com"]
    start_urls = ["https://news.163.com/"]

    def start_requests(self):
		# 使用 splash 的请求
        
        yield SplashRequest(
            url=self.start_urls[0], # 可以不写 在 lua_source写死就行
            endpoint="execute",
            args={
                "lua_source": lua_source
            },
            callback=self.parse
        )


    def parse(self, response):

        print(response.text)
```

```python
setting.py 配置

# 我的splash ip
SPLASH_URL = "http://192.168.164.32:8050"
# splash 的需要的 download中间件
DOWNLOADER_MIDDLEWARES = {
   "scrapy_splash.middleware.SplashCookiesMiddleware": 723,
   "scrapy_splash.middleware.SplashMiddleware": 725,
   "scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware": 810
}
# splash 的去重过滤器
DUPEFILTER_CLASS = "scrapy_splash.SplashAwareDupeFilter"
# # splash 的http缓存 
HTTPCACHE_STORAGE = "scrapy_splash.SplashAwareFSCacheStorage"
```



