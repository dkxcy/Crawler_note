# 增量式爬虫

爬取天涯论坛

```python
import scrapy
from chua.items import TianyaItem
from redis import Redis

class TianyaSpider(scrapy.Spider):
    name = "tianya"
    allowed_domains = ["tianya.com"]
    start_urls = ["https://tianya.com"]

    def __init__(self, name=None, **kwargs):
        self.red = Redis(host="127.0.0.1", port=6379, db=14, password="123456")
        super(TianyaSpider, self).__init__(name, **kwargs)

    def parse(self, resp, **kwargs):
        tbodys = resp.xpath("//tbody")[1:]

        for tbody in tbodys:
            hrefs = tbody.xpath("//@href").extract()

            for href in hrefs:
                detail_href = resp.urljoin(href)
                result = self.red.sismember("ty:request", detail_href)

                if not result:
                    print("已经抓取:" + detail_href)
                    continue

                yield scrapy.Request(
                    url=detail_href,
                    callback=self.parse_detail
                )
        next = resp.xpath("//next/@href").extract_first()

        if next:
            yield scrapy.Request(
                url=resp.urljoin(next),
                callback=self.parse
            )

    def parse_detail(self, resp, **kwargs):

        t = TianyaItem()
        title = resp.xpath("").extract_first()
        content = resp.xpath("").extract_first()
        t["title"] = title
        t["content"] = content
        self.red.sadd(resp.url)
        yield t
```

items

```python
import scrapy


class TianyaItem(scrapy.Item):

    name = scrapy.Field()
    content = scrapy.Field()
```



pipelins 存储

```python
from itemadapter import ItemAdapter
from redis import Redis
from json import dumps

class ChuaPipeline:
    def process_item(self, item, spider):
        # 存储到item
        r = self.rd.sadd("ty:items", dumps(dict(item)))
        if r:
            print("存储到数据库", item["title"])
        else:
            print("已经存储", item["title"])

        return item

    def open_spider(self, spider):
        self.rd = Redis(host="127.0.0.1", port=6749, password="123456", db=14)

    def close_spider(self, spider):
        self.rd.close()
```





# 分布式爬虫

安装scrapy-redis

```
pip install scrapy-redis

# 创建一个爬虫类
scrapy genspider ty tianya.com
```



使用 `scrapy-redis`

```python
class DuSpider(RedisSpider):
    name = "du"
    allowed_domains = ["163.com", "baidu.com"]
    # start_urls = ["http://www.baidu.com"]
    redis_key = "du:start_url"


    def parse(self, response, **kwargs):
        hrefs = response.xpath("//a/@href").extract()
        page_cookie = "需要就写"
        cookies = {}
		# 配置cookies
        for cookie in page_cookie.split("; "):
            k, *v = cookie.split("=")

            if len(v) > 1:
                cookies[k] = "=".join(v)
                continue

            cookies[k] = v[0]
            
        for href in hrefs:

            yield scrapy.Request(
                url=href,
                callback=self.parse_detail,
                cookies=cookies
            )

      
```





使用 `scrapy_redis.pipelines`

```python
# 添加scrapy_redis 的pipeline
ITEM_PIPELINES = {
    "scrapy_redis.pipelines.RedisPipeline": 301
}
```

redis相关配置

```python
# redis相关配置
REDIS_HOST = "127.0.0.1"
REDIS_PORT = 6379
REDIS_DB = 4
REDIS_PARAMS = {
	"password": "123456"
}

# scrapy_redis 相关配置
SCHEDULER = "scrapy_redis.scheduler.Scheduler" # 调度器
SCHEDULER_PERSIST = True # 断点续爬
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter" # redis过滤器类
```



**scrapy-redis 默认存储到redis的三个项**

```
dupefilter  当页面请求成功,请求url的hash值会存储到redis里面	 baidu:dupefilter    dont_filter=True的请求不会存储
items       当页面传递参数item到 pipeline 会自动存到  		baidu:items
request     页面请求的队列,请求结束会自动删除          		  baidu:request 请求结束会从redis自动删除
```



-----



# 布隆过滤器

安装布隆过滤器

```
pip install scrapy_redis_bloomfilter
```

**布隆过滤器的 settings配置** 

```python
# scrapy改用 布隆过滤类
DUPEFILTER_CLASS = "scrapy_redis_bloomfilter.dupefilter.RFPDupeFilter"
# 哈希函数的个数, 默认为6, 可以改
BLOOMFILTER_HASH_NUMBER = 6
# BloomFilter 的 bit 参数, 默认30, 占用 128MB 空间, 去重量级 1 亿
BLOOMFILTER_BIT = 30
```

配置完可以使用了, 不用其他的代码





# 遇到的问题



scrapy 版本 和 scrapy-redis 版本 不兼容带来的问题

<p style="white-space: pre"> <b style="color: red"> WARNING</b>
    <i  style="color: red">`ExecutionEngine.crawl() got an unexpected keyword argument 'spider'` </i>报错了

  

方案1: 改用scrapy2.9

方案2: 修改 schedule_next_requests

```python
def schedule_next_requests(self):
        """Schedules a request if available"""
        # TODO: While there is capacity, schedule a batch of redis requests.
        for req in self.next_requests():
            #  这是由于 scrapy-redis 传递(req, spider) 两个参数 但是 scrapy2.11中不接收 spider参数导致的 
            # self.crawler.engine.crawl(req, spider=self)
            self.crawler.engine.crawl(req) # 改成这个
```





