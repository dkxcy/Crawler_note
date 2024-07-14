# Thread 多线程

**线程**

```python
from threading import Thread
    
    
# 创建线程, target 任务   ,args 任务参数是个 tuple
t1 = Thread(target=func, args=("周杰伦",))

# 开启线程
t1.start()
```

**线程池**

```python
# 线程池引入 
from concurrent.futures import ThreadPoolExecutor

# 创建线程池
with TreadPoolExecutor(10) as t

	# 提交任务到线程池, 后续是参数 ,会自动执行
	t.submit(fn, 1,2,3)
    
    # 一次提交多个任务, 如果需要使用的话
    t.map(fn, ["周杰伦"每个数组对应一个任务参数],[1])
```









# Process 多进程

**进程**

```python
# 引入进程 
from multiprocessing import Process

#操作与线程一致
```



**进程池**

```python
#引入进程池 
from concurrent.futures import ProcessPoolExecutor

#操作与线程一致
```



队列

注意: 

​	**进程和线程并用, 不推荐, 如简单的函数嵌套就可以实现**



# Queue 队列

用于线程之间,数据交互



```python
def fn1(q, ):
    
    q.put(数据)
    # 结尾加一个 q.put("close")

def fn2(q):
    
    data = q.get()
    
    if data == "close":
        print("判断是否退出")
    	return 

q = Queue() 创建队列

# 传入q对其进行操作, fn会接收到q 
p1 = Process(target=fn1, args=(q,)) 
p2 = Process(target=fn2, args=(q,)) 

```







# 协程

asyncio 协程模块

```python 
# asyncio 异步模块  aiohttp异步请求 aiofiles异步存储
pip install asyncio aiohttp aiofiles

async def download_ts(url):

    try:
        session = aiohttp.ClientSession()    
        resp = session.get(url, timeout=200)
        content = await resp.content.read()
        
        f = aiofiles.open("1.ts", mode="wb")
        await f.write(content)
    except:
        print("错误")
    	

async def run():
	tasks = []
	for item in urls:
	
		task = asyncio.createtask(download_ts)
        tasks.append(task)

	await asyncio.wait(tasks)
    
    # result = await asyncio.gather(*tasks)结果会返回到result 
    # return result

if __name__ == '__main__':

    result = asyncio.run(run())
    print(result)

```

