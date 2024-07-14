# mysql



安装mysql







## MySQL使用



**创建数据库**

```sql
create database spider
```



表格

```sql
-- 创建表格
create table stu(
	sno int(5) not null primary key auto_increment,
	sname varchar[5] ,
	sage varchar[5]

);

-- 删除表格
drop table stu;

-- 表格重命名        
alter table stu rename to school
```

表格的其他操作

```sql
-- 添加一列
alter table stu
add column sdate varchar(30) -- 添加sdata列


-- 删除一列
alter table stu
drop column sdate  -- 删除 sdate列


-- 改变一列 数据类型
alter table stu
modify column sname int(5)


```



### 表内操作 

增删改

```sql
-- 添加数据
INSERT INTO table_name(coll, col2) values (vall, val2, val3)

INSERT INTO stu(name, age, class, school) values
("张三", 1, "一年三班", "光明幼儿园"),
("李四", 1, "一年三班", "光明幼儿园"),
("王五", 1, "一年三班", "光明幼儿园"),
("赵六", 1, "一年三班", "光明幼儿园");

```

更新数据

```sql
-- 修改
update stu set sage = 10, sname = "李四" where sno = 1
```

删除数据

```sql
-- 删除
delete from stu where sno = 4
```





查询

```sql
-- 条件查询  
--  > < = !=  
select * from stu where sage = 28;

-- in(a,b) 包含
select * from stu where sage in(28,29)

-- 逻辑 and or
select * from stu where sage = 28 or sage = 30

-- like 模糊查询  %模糊范围 %n n前面模糊
select * from stu where sname like "%李"

-- between 区间查询 28~56
select * from stu where age bewteen 28 and 56
```



查询结果上的操作

```sql
-- 重新命名查询结果
-- select 查询结果 as 姓名 from stu 
select sname as "姓名", school as "学校", from stu;


-- 计算结果
-- avg(类型) sum() count max() min() 拿出数据进行计算 要么 group by 分组计算 要么整体计算 不可以又查数据又计算算
select class, avg(score) from stu group by class 


-- having 结果之后在进行最后筛选, 相当于再次where
select * from stu having class = "一年二班";



-- order 类型 asc\desc 对于结果进行排序
select * from stu order by score desc

-- limit n,m   从n开始取m条数据
select * from stu limit 2,3;


```



分组后查询

```sql
-- group_by 类型     通过列的数据不同进行分组 用什么分作 返祖结果必须包含 class, 只有分组结果没有数据, 可以avg 统计 不能返回实际数据
select class as 班级 ,avg(score) from stu group by class;
```



## python 链接mysql



```python
# 安装mysql包
pip install pymysql


# 创建连接
conn = pymysql.Connect(
	user = "root",
    password = "123456",
    host = "localhost",
    database = spider, # 连接那个数据库
    port = 3306
)

# 创建游标
cursor = conn.cursor()

# sql语句
sql = "insert into stu(sname, sage, score, class) values" \
"('张铁柱', 18, 94, '一年三班')"

# 执行sql
cursor.execute(sql)

# pymysql 默认开始事务 默认和报错就rollback 提交要手动 commit()
#提交事务
conn.commit()


if cursor:
    cursor.close()
if conn:
	conn.close() # 关闭连接
```





实例

```sql 
def conn():
	 conn = pymysql.connect(
            user = "root",
            password = "123456",
            host = "localhost",
            database = spider, # 连接那个数据库
            port = 3306
        )
    return conn


def add(sql):

	try: 
        conn = connect()

        cursor = conn.cursor()

        cursor.execute(sql)

        conn.commit()
        # 新增的数据id值
        new_id = cursor.lastrowid
        return new_id
        
    except: 
    	conn.rollback()
    finally:
    	cursor.close()
    	conn.close()

def add(sql):

	try: 
        conn = connect()

        cursor = conn.cursor()

        cursor.execute(sql)

        conn.commit()
    except: 
    	conn.rollback()
    finally:
    	cursor.close()
    	conn.close()
```



change统一 代码

```
def change(sql, isInsert=False):
	try: 
        conn = connect()

        cursor = conn.cursor()

        cursor.execute(sql)

        conn.commit()
        # 新增的数据id值
        if isInsert:
        	new_id = cursor.lastrowid
        	return new_id
        
    except: 
    	conn.rollback()
    finally:
    	cursor.close()
    	conn.close()
    	
 def add(sql):
 	
 	return change(sql, True)
 	
 def update(sql):
 	return change(sql)
 
 def delete(sql):
 	return change(sql)
```



pymysql 查询

```python
from pymysql.cursor import DictCursor
# 返回结果是字典游标

def select(sql):
    
    conn = connect()
 	
    # 控制游标返回结果为字典 cursor=DictCursor
    cursor = conn.cursor(cursor=DictCursor)
    
    # result 没有数据返回的是返回的长度
    result = cursor.execute(sql)
    
    # 返回的数据在cursor里面
    one = cursor.fetchone() #返回一条
    all = cursor.fetchall() # 返回所有
    all = cursor,fetchmany(2) # 返回n条, 是一个迭代器
    
```











## 使用

```mongodb
# 查询当前数据库
> db


# 查看所有数据库
> show dbs


# 切换数据库
> use python
```

插入数据

```
# 直接往数据库插入数据, 如果数据库不存在则创建
# 打开 stu数据库
use stu
# 数据库建一个 a集合 插入数据
stu.a.insert({})

# 使用数据stu 然后把stu数据库删除
use stu
db.dropDatabase()

```



集合的创建

```
use syalr

# 创建一个集合   size最大数量是255, capped 滚动的集合会循环利用
db.createCollection("xxx_log", {capped: true, size: 255})


# 删除集合
db.xxx_log.drop()
```





增删改查

增

```python
# 添加
db.stu.insert({sno: "10010", sname: "tory", sage=18})


# 查stu集合 所有
db.stu.find()


# 添加多条
db.stu.insert([
    {sno: "10011", sname:"李四", bson: "log"},
    {sno: "10021", sname:"王刚"}
])
```

改

```
#   multi 是true 则改多条, 默认false改一条
db.stu.update({查询条件}, {待修改内容}, {multi: 是否改多条数据})

# 改多个数据   $set  如果没有set则会把整条数据改了   $set 只会改 set后的数据
db.stu.update({sname: "王刚"}, {$set: {sage: 155, sname: "李四"}}) # 改了 sage 和 sname
```

删除

```
# 删除一个
db.stu.deleteOne({_id: ObjectId("dsafsadfdad7")})

# 删除多个
db.stu.remove({})
```







查询

```python
# 查询一条
db.stu.find({name: "朱"})

# 查询结果 保留 sname, sage
db.stu.find({}, {sname: 1, sage: 1})


# 查询的逻辑
# $gt 大于 $lt

db.stu.find({sage: {$gt: 18}})

# $and: [{条件1}, {条件2}] 查询多个条件
db.stu.find({$and: [{age: {$gt: 33}, {$lt: 100}}]})

# $or: [{条件1}, {条件2}]
db.stu.find({$or: [{age: {$lt: 100}, {age: {$gt: 20}}}]})


# skip(n) 跳过n条 和 limit(n) 截取n条 
db.stu.find().skip(3).limit(2)


# 排序 sort({sage: 1}) 1升序 -1降序
db.stu.find().sort({age: 1})

# count() 统计 数量
db.stu.find().count()
```







# pymongo

安装

```
pip install pymongo


# mongo这个命令可以在cmd 查询到端口号
> mongo
```





## mongodb使用

```
from pymongo import MongoClient

def get_db()
	client = MongoClient(
		host="localhost",
		port=27017
	)
	
	# 进入那个数据库
	db = client["syalr"]
	
	return client
```

增删改

```python
def add_one(data):
    
    db = get_db()
    
    result = db["stu"].insert({"sname": "王刚", age: 100})
	return result 


```

























# Redis 3.0.504的使用





安装

下载安装包解压

```
# 下面再解压路径cmd
# 安装redis到 window服务上
redis-server.exe --service-install redis.window.conf --loglevel verbose

# 启动redis服务
redis-server --service-start
```







使用redis

```
redis-cli # 进入redis命令行

auth 123456   # 注意: requirepass 123456 在 conf文件改一下密码

select 1   # 切换到 数据库 1
```



添加基本类型的数据

```
set name value  添加一段数据

get name  查询 name关键字的数据
incr name 给 name自增
```

添加 hash 数据 (字典)

```
hmset obj key value key2 value2  添加数据

HGET obj name  查询某个关键字的数据
```

添加 set 集合数据

```
sadd setobj 123 添加数据

srandmember keyobj 返回集合随机个元素
sismember setobj 123 判断集合是否有123

smembers setobj  返回集合所有数据

```

添加list 列表数据(注意他是一个双向列表)

```
lpush list1 1  添加数据

blpop list1  从list1获取一条, 并移除
lindex list1 1 通过索引获取数据

llen list1  获取长度
```



注意 window 结束要save一下



## python redis

安装redis

```
pip install redis
```



使用 连接redis

```
from redis import Redis

# 连接 redis
redis_connect = Redis(
	host="localhost",
    port=6379,
    username="auth",
    password="123465",
    db=1, # 选择数据库
    decode_response="utf-8"
)
```



```python
#基本数据的 获取
redis_connect.set('name', "张三")
redis_connect.get("name")

# hm字典的 添加和获取
redis_connect.hset("hmobj", "name", "zhangsan")
redis_connect.hget("hmobj", "name") 
redis_connect.hgetall("hmobj") # 获取所有

# set 集合 添加和获取
.sadd("nameset", "zhangsan") 添加
.srandmember("nameset") # nameset 随机获取一个
.sismember("nameset", "zhangsan") 判断是否有zhangsan
.smembers("nameset") 获取所有值

# list 列表注意他是给双向列表   [456,123] 先加入的在后, => 每次加进去的往后移动
.lpush("list1", 1) 添加数据
.lpop("list1") 获取一条 从左边拿
.rpop("list1") 获取一条 从右边拿
.llen("list1") 获取长度
.lindex("list1", 1)     这是根据加入的先后顺序拿到的, 数字是先后顺序
```













# 案例: 爬虫二手房





```
import requests
from lxml import etree 

# 页面资源
def get_page_source(url):

	resp = request.get(url)
	
	return resp.text
	

# 页面资源追踪
def parse(source):
	
	html = etree.HTML(source)
	
	lis = html.xpath("//ul/li")
	data_list = []
	for li in lis:
		dic = {}
		title = li.xpath("./div[1]/div[1]/a/text()")[0]
		position = li.xpath()
        house = li.xpath()[0]
        house = house.split("|")
        
        if len(house) == 6:
        	house.insert(5, "")
        if len(house) == 8:
			house.pop()
			
		dic = {
			"title": title,
			"position": position,
			"mainji": mainji,
			"house": house,
			"tags": tags 
		}	
		
		data_list.append(dic)
	
	return data_list
		

if __name__ == "main":

	url = ""
	
	page_source = get_page_source(url)
	
	data_list = parse(page_source)
	
	# 存到mongodb里面
    mongodb.add_many("house", data_list)
	
    
    # 存到 mysql里面 方法1 使用 executed()
    
    cursor.executed("create table house(
    	title varchar(255) not null ,
        position varchar(255) not null,
        mainji varchar(255),
        house varchar(255),
        tags varchar(255
    )")
    
    conn.commit()
    
    
    for item in data_list:
    
    	cursor.executed("insert into house(title, position, mainji, house) values
    	(item.title, item.position, item.mainji, item.house, ",".join(item.tages)")
    conn.commit()
    
    
    # 存到mysql 方式2 使用 executemany(sql, list)
    
    
    sql = "insert into house(title, position, mainji, house) values 
    
    		(%s, %s, %s, %s)"
    
    for item in data_list
    
    	item["tags"] = ",".join(item["tags"])
   	
   	
   	lst = (tuple(dic.values()) for dic in data_list) # 优化一下, 占的位置更小
   	
   	cursor.executemany(sql, data_list)
    
    conn.commit()
```

