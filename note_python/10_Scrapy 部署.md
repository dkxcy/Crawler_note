# wangxiao 总结





编写一个py用于统一处理文件

```
from redis import Redis 


client = Redis(

)

def process_img():
	
	


def main():

	while 1: 
		
		item = client.blpop("xiao:url:item")
		
		item.


```







# scrapy 部署

安装python源文件的环境

```
yum install gcc patch libffi-devel python-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlit-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel python36-devel -y
```







下载python

```
yum install -y wget # 先下载wget下载器

wget https://www.python.org/ftp/python/3.8.10/python-3.8.10.tgz
```



解压安装包

```
tar xvf Python-3.8.10.tgz
```



安装

```
./configure --prefix=/usr/local/python
make && make install
```



## 方式1

安装各种python包

```
# 方式1 
程序要什么就一个个 install

# 方式2
windows中命令行 reguirement.text 命令得到所有的包

# 把上面的文件放到linux里面
可以给pip换个源, 速度更快
# 安装所有包
pip3 install -r requirement.txt
```



```
scrapy crawl wangxiao # 运行程序
```











## 方式2

linux安装 scrapyd

```
pip3 install scrapyd
```

配置scrapy 远程服务

```
vi /usr/local/python/lib/python3.8/site-packages/scrapyd/default_scrapyd.conf


修改远程的localhost
bind_address = 0.0.0.0

:wq

```











配置linux防火墙

```
iptables -I INPUT -p tcp -dport 6800 -j ACCEPT #开启6800端口
```

此时windows就可以访问6800端口了

```
chrome

https://192.168.64.132:6800
```

