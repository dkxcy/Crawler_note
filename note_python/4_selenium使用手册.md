# selenium 



selenium 安装

```
cmd
# 安装selenium
pip install selenium

# 在本地安装 谷歌驱动包
chrome_driver
把exe文件 放到项目里面或 环境变量
```





selenium 使用



开始使用打开浏览器

```python
from selenium.webdriver import Chrome
from selenium import webdriver

# 配置路由信息
options = webdriver.ChromeOptions()
# 配置属性避免直接关闭
options.add_experimental_option("detach", True)


Chrome = webdriver.Chrome

# 打开浏览器
web = Chrome(options=options)

web.get("http://www.baidu.com")
```





web上的操作

```python


# 查询标签操作 , 返回el标签
el = web.find_element(by=By.XPATH, value="")

# 查询多个标签操作
els = web.find_elements(by=By.XPATH, value="")

#by.ID 可以查询id 属性

# el可以继续进一步操作
el.click()# 点击
el.send_keys("python", Keys.ENTER) # 输入操作 然后回车


# for 循环每一个标签
for el in els:
    # el 可以继续查
    el.find_element(by=By.XPATH, value="")
    
    
# web执行 js脚本
web.execute_script("""
	var a = document.querySelect(".up-login-banner")
	
	a.parentNode.removeChild(a)
""")

# web窗口切换 选择web.window_handers最新的窗口
web.switch_to.window(web.window_handles[-1])
# 关闭当前窗口
web.close()
# web窗口切换 到原窗口
web.switch_to.window(web.window_handles[0])


# 从el 拿到数据 拿到文本,整个el里面的文本
ele.text  
# 从 el 拿到 属性的值
ele.get_property("placeholder")


# 获取页面源代码 动态的
web.page_source

# 关闭浏览器
web.quit()
```

拓展

```
Keys 键盘操作键位
keys.ENTER 回车
Keys.SPACE 空格


注意: 当窗口切换时候需要停一会因为代码跑的速度比网络快
time.sleep(1)
```



页面的ifram页面切换

```python
# 拿到iframe
frame = web.find_element(by=By.XPATH, value="iframe")

# 切换到iframe当中
web.switch_to.frame(frame)
# 此时查询就是从frame 开始查询
web.find_element

# 跳出iframe  回到frame的上一级   
web.switch_to.parent_frame()
```





Select 标签操作

```python
# 导入Select
from selenium.webdriver.support.select import Select

# 得到 sele 
sel = Select(web.find_element(by=XPATH, value="//select"))


#sel 的操作
len(sel.options) #查看一共几项

# 选择第几项
sel.select_by_index(1)
# 选择value为t的 项
sel.select_by_value("2022")
# 选择文本为 t 的选项
sel.select_by_visile_text("2023年")


# 点击完等他一下获取数据
time.sleep(3)
# 拿到标签提取数据
tr = web.find_element(by, value="//tr")
tr.text
```



selenium 浏览器警告问题

```python
from selenium.webdriver.chrome.options import Options

opt = Options()
opt.add_argument("--header")
opt.add_argument("--disable-gpu")
```





# 验证码图片破解工具

## 超级鹰的使用

```python
# 导入超级鹰
from chaojiying import Chaojiying_Client


# 拿到要破解的题目
png = web.find_element().screenshot_as_png()


# 创建一个超级鹰 用户\密码\密钥
chaoji = Chaojiying_Client("saf", "asdf", "asf")


# 传入图片, 破解
result = chaoji.PostPic(png, 1902)

# 登录
web.find_element().send_keys() #用户名
web.find_element().send_keys() #密码
web.find_element().send_keys() #验证码
web.find_element().click()
```

拓展

```python
#打印页面元素
el = web.find_element()

# 将元素打印保存到 内存里 "./dir"
el.screenshot("./1.png")

#拿到元素图片的字节 bytes
el.screenshot_as_png

# 拿到元素的图片 base64 的字节码
el.screenshot_as_base64
```



## 图鉴使用

查看api, 每个网站破解都不一样



图片点击验证码

```python
# 事件链 工具
from selenium.webdriver.common.action_chains import ActionChains



# 发送给图鉴 得到坐标
verify_code = base64_api(tu)

# 得到每一个要点选位置xy轴, 分别提取出来
for p in verify_code.split("|"):
	x = int(p.split(",")[0])
	y = int(p.split(",")[1])
	
	
# 使用 ActionChains()移动光标

#verify_div 左上角是基准点
# xoffset 是 光标移动的 x单位距离
# yoffset 是 光标移动 y单位距离
   ActionChains(web).move_to_element_with_offset(verify_div, xoffset=x, yoffset=y).click().perform()
```











# 关于等待

```python
# 必须等待 3秒
time.sleep(3)

# 最多等待 10秒
implicitly_wait(10)

# 强制等待 要导包 一共三个包
from selenium.webdriver.support.ui import WebDriverWait #wait本身
# 查询方式 包
from selenium.webdriver.common.by import By
# 检测 工具
from selenium.webdriver.support import expected_conditions as EC


#使用 until 结束等待条件 
WebDriverWait(web,10,0.5).until(
	# EC来回验证 是否出现input 如果出现就继续执行,最终如果没有就报错
  EC.presence_of_element_located((By.XPATH, "//input"))
)
```



### 12306

```
# 实现拖拽  perform() 提交事件
ActionChains(web).drag_and_drop_by_offset(btn, xoffset=300, yoffset=0).perform()

ActionChains(web).click_and_hold().move_by_offset()
```



网站检测到当前是selenium 控制的浏览器



```
opt = Options()
# selenium检测的 模拟正常浏览器
opt.add_argument("--disable-blink-features=AutomationControlled")
```

