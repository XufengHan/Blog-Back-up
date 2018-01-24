---
title: python爬虫
date: 2017-11-29 17:45:11
tags: python Beautifulsoup requests
toc: true
---

## 使用工具
[anaconda-python3.6](https://www.anaconda.com/download/)
安装完成并配置好环境之后，我们就可以使用python了，anaconda里包含了许多常见的python工具。

## 解析HTML网页
   米开朗基罗被问及如何完成《大卫》这样匠心独具的雕刻作品时，他有一段著名的回答：“很简单，你只要用锤子把石头上不像大卫的地方敲掉就行了。”
   解析HTML网页也是相似的工作，我们要获取的数据一般都隐藏在复杂的HTML源码里，敲掉不需要的数据，目标数据就出来了。
 
### 一个html网页源码示例

下面的标签是[2017年豆瓣网最新电影](https://www.douban.com/tag/2017/movie)“寻梦环游记”的html标签，对比这个电影的页面信息，我们
可以看到，我们想要的内容，比如电影名字，标签，评分等，都在html源码里，html源码在网页里点击右键或者按Ctrl+Shift+i和F12键就可以查看了。就可以查看了。
```s

<dl>
    <dt>
      <a href="https://movie.douban.com/subject/20495023/?from=tag_all" target="_blank">
        <img src="https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2503997609.webp" />
      </a>
    </dt>
    <dd>
      <a href="https://movie.douban.com/subject/20495023/?from=tag_all" class="title" target="_blank">寻梦环游记 Coco</a>
        
      
      <div class="desc">
        美国 / 喜剧 / 动画 / 冒险 / 音乐 / 家庭 / 2017 / 李·昂克里奇 / 安东尼·冈萨雷斯 / 盖尔·加西亚·贝纳尔 / 本杰明·布拉特
      </div>
    
        <div class="rating">
          <span class="allstar45"></span>
          <span class="rating_nums">9.2</span>
        </div>
    </dd>
  </dl>
  
```



![pic1](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/pachong/douban_movies.png)



### 使用锤子
把Beautifulsoup比喻成锤子我觉得有点不太合适，我倒是更觉得它的功能更像一个漏斗，用正则表达式才比较像在用锤子。

下面是我用python写的爬虫代码，第一至十二行是导入request,和Beautifuisoup等库，第13行我们下载了网页的源
码，第14行使用Beautifulsoup进行了解析，15,16,17行提取了在标签 ”<a class="title>" 里的文字，豆瓣把所有
的电影名称信息都放在这个标签里了，所以我们得到了这个页面所有的电影名称。


```python
from urllib.request import urlretrieve
import requests
from bs4 import BeautifulSoup
from os import remove
try:
    import cookielib
except:
    import http.cookiejar as cookielib
try:
    from PIL import Image
except:
    pass
html = urlopen("https://www.douban.com/tag/2017/movie")
douban = BeautifulSoup(html,"lxml")
nameList = douban.findAll("a", {"class":"title"})
for name in nameList:
 print(name.get_text())
 
#寻梦环游记 Coco
#至爱梵高·星空之谜 Loving Vincent
#王牌特工2：黄金圈 Kingsman: The Golden Circle
#小丑回魂 It
#英伦对决 The Foreigner
#银翼杀手2049 Blade Runner 2049
#猎场
#心灵猎人 第一季 Mindhunter Season 1
#伯德小姐 Lady Bird
#怪奇物语 第二季 Stranger Things Season 2
#雷神3：诸神黄昏 Thor: Ragnarok
#九州·海上牧云记
#请以你的名字呼唤我 Call Me by Your Name
#敦刻尔克 Dunkirk
#全球风暴 Geostorm
```

## 采集过程

### 获取子页面
主页面的信息有时候满足不了我们的需求，就像我们刚才提取的页面，点击电影名称就可以跳到下图页面。
![pic2](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/pachong/page.png)

这个页面有着更详细的电影信息，而且它的链接也储存在了上一个页面的源码之中。对网页的采集过程有了了解后，获取子页面就很简单了。

```python
for link in nameList:
    print(link.attrs['href'])	
#https://movie.douban.com/subject/20495023/?from=tag_all
#https://movie.douban.com/subject/25837262/?from=tag_all
#https://movie.douban.com/subject/26378579/?from=tag_all
#https://movie.douban.com/subject/3604148/?from=tag_all
#https://movie.douban.com/subject/25723583/?from=tag_all
#https://movie.douban.com/subject/10512661/?from=tag_all
#https://movie.douban.com/subject/26322642/?from=tag_all
#https://movie.douban.com/subject/26691486/?from=tag_all
#https://movie.douban.com/subject/26588314/?from=tag_all
#https://movie.douban.com/subject/26844438/?from=tag_all
#https://movie.douban.com/subject/25821634/?from=tag_all
#https://movie.douban.com/subject/26322999/?from=tag_all
#https://movie.douban.com/subject/26799731/?from=tag_all
#https://movie.douban.com/subject/26607693/?from=tag_all
#https://movie.douban.com/subject/22266012/?from=tag_all
```
看一看上面的提取超链接链接的python代码，没错，超链接和电影名称都在下面这个节点里，herf标签的属性就是它的超链接。得到这些链接的地址之后，我们就可以根据它继续进行网页的爬取了。
```s
<a href="https://movie.douban.com/subject/20495023/?from=tag_all" class="title" target="_blank">寻梦环游记 Coco</a>
```
可能在这里有人要问，为什么不直接提取这个页面呢？原因就是，在采集网站的过程过程中，大多都要遵循一些步骤，要按照一个节点到另一个节点的顺序来，大概就像树的根->枝->叶这个过程。

### 网页翻页
在我们兴高采烈的准备开始网站采集过程的时候，发现豆瓣里2017年的电影好像有20页（好了，我知道前20页有几部不是2017年的，原因问豆瓣去，我不知道），这个问题还没有处理呢，用刚才提取超链接的方法好像不行，网页源码里好像没有下一页的地址。
其实这个问题很容易解决，大家看看第一页，第二页，和第三页的网址，发现了什么没？每翻一页start=后面的数字就加了15，而我们每一页正好15部电影，没错这个start参数的意思就是从第几部电影开始，?是参数的连接符。我们以后就会了解到，这其实就是一个post操作。（不是我要用英文装逼，是没有看到什么好的翻译，用英文形象多了）
```s
https://www.douban.com/tag/2017/movie  #第一页
https://www.douban.com/tag/2017/movie?start=15 #第二页
https://www.douban.com/tag/2017/movie?start=30 #第三页
```

## 数据清洗
为什么要数据清洗？比如大家执行一下下面的代码去得到第一部电影的标签，你会发现什么鬼，为什么结果还有斜杠杠，那我怎么分词，怎么统计词频，怎么查看电影的关联性，怎么做主题模型呢！
```python
nameList = douban.findAll("div", {"class":"desc"})
print(nameList[1].get_text())

#英国 / 剧情 / 传记 / 动画 / 2017 / 多洛塔·科别拉 / 道格拉斯·布斯 / 杰罗姆·弗林 / 罗伯特·古拉奇克
```
在你一筹莫展之际，其实你应该庆幸，这其实是最理想的结果，因为从上图来看，网页是上的数据的确是这个样式，我们的结果还没有出现偏差。
对了，不要指望我会什么做分词，统计词频，计算相关性，建立主题模型，豆瓣的数据拉下来后我还没有想到什么好的分析内容。

### 用字符处理函数清洗
这里我就使用了一个split，就和他的单词含义一样，split就是将字符串按照" / "拆分，结果返回一个列表。
python自带的字符处理方法大概还有，替换，删除，截取，复制，连接，查找这些逻辑方法，把字符拆分组装成不同的形式。
```python
name=nameList[1].get_text().split(" / ")
print(name)
#['英国', '剧情', '传记', '动画', '2017', '多洛塔·科别拉', '道格拉斯·布斯', '杰罗姆·弗林', '罗伯特·古拉奇克']
```

### 用正则清洗
[正则表达式资料](http://www.runoob.com/regexp/regexp-syntax.html)
[在线正则表达式测试](http://tool.oschina.net/regex/)

如果某个问题要用到正则，实际上它就成了俩个问题。看看下面的python代码，你可能觉得不就是用了个替换，把" / "换成了空白吗？正则表达式就是定义这些匹配规则的。
```python
name=nameList[1].get_text()
print(name.replace(" / "," "))
#英国 剧情 传记 动画 2017 多洛塔·科别拉 道格拉斯·布斯 杰罗姆·弗林 罗伯特·古拉奇克
```
我们的项目好像用不着正则，不过还是可以测试一下，用正则去掉中文。经测试用replace调用正则失败了（原因不明），所以我又调用了sub和re里的compile。
```python
import re
name=nameList[1].get_text()
p=re.compile("[\u4e00-\u9fa5]")
print(p.sub('', name))
#/  /  /  / 2017 / · / · / · / ·
```
## 储存数据

### 导出为csv文件
csv文件是以“,”分隔的一种文本文件，用下面的代码就可以把我们爬取到的电影名称和电影页面数据保存到本地。
首先导入了pandas数据分析包，然后按照之前说过的内容提取出电影名称和网页链接，再合并成数据框保存到本地。
我在数据框里只保存了电影名称列，页面链接我作为行标题保存了，有没有觉得匠心独运。然而事实是我对用pandas处理数据并不熟练，不太明白怎么会成这样。
```python
import pandas as pd
nameList = douban.findAll("a", {"class":"title"})
for i in range(0,15):
  nameList[i]=nameList[i].get_text()
linkList = douban.findAll("a", {"class":"title"})
for i in range(0,15):
  linkList[i]=web[i].attrs['href']    
data = pd.DataFrame(nameList,linkList,columns = ['name'])
data.to_csv('C:/Users/Administrator/Desktop/douban_movie.csv') #这里需要自己设置保存路径
```
### 导出到数据库
python直接导入到数据库可是天坑满满，我的方案是导出为csv这样的文本文件，再用kettle或者什么ETL工具导入到数据库，可以参考我之前写的一个kettle样例，把表输入改成csv输入就行了。
[kettle案例](http://www.hanxufeng.club/2017/11/24/ETL-kettle/)

## 模拟登陆
[案例分享](https://github.com/XufengHan/fuck-login)话不多说，先上案例。额！不是我写的，但是肯定是比我写的会详细，毕竟是一个开源小项目的标准。
模拟登陆的额意义在于采集登陆窗口背后的信息，上面的示例中的网络爬虫在和大多数网站的服务器进行数据交互时，都是用HTTP 协议的 GET 方法去请求信息。现在我要说的是 POST 方法，即把信息推送给网络服务器进行存储和分析。
下面是一组模拟登陆的案例代码
```python
url = 'https://accounts.douban.com/login'
datas = {'source': 'index_nav',
         'remember': 'on'        
        }
# 输入账号密码
datas['form_email'] = input('Please input your account:')
datas['form_password'] = input('Please input your password:')
login_page = session.post(url, data=datas)
```
最理想的情况这样就能够登陆了，输入网址账号密码，就能完成登陆信息了。

### 验证码登陆
虽说豆瓣是一个很良心的网站，在访问方面没有多大限制，但是验证码还是有的，如果你第一次没登陆成功的话。下面有个验证码识
别章节，和这部分的内容的区别可以看名字，这里的验证码是人工识别的。

#### 准备内容
```python
url = 'https://accounts.douban.com/login'
datas = {'source': 'index_nav',
         'remember': 'on',        
        }
# 输入账号密码
datas['form_email'] = input('Please input your account:')
datas['form_password'] = input('Please input your password:')
# 设置标头，模仿浏览器
headers = {'Host':'www.douban.com',
           'Referer': 'https://www.douban.com/',
           'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0',
           'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
           'Accept-Language': 'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',
           'Accept-Encoding':'gzip, deflate, br'}		   		   
```
上面的代码加了一个标头部分，虽然豆瓣用不着，但是为了代码的可重复性，我还是加上了。
```

#### 构造获取验证码函数
def get_captcha():
    '''
    获取验证码及其ID
    '''
    r = requests.post(url, data=datas,headers=headers)
    page = r.text
    soup = BeautifulSoup(page, "html.parser")
    # 利用bs4获得验证码图片地址
    img_src = soup.find('img', {'id': 'captcha_image'}).get('src')
    urlretrieve(img_src, 'captcha.jpg')
    try:
        im = Image.open('captcha.jpg')
        im.show()
        im.close()
    except:
        print('到本地目录打开captcha.jpg获取验证码')
    finally:
        captcha = input('please input the captcha:')
        remove('captcha.jpg')
    captcha_id = soup.find(
        'input', {'type': 'hidden', 'name': 'captcha-id'}).get('value')
    return captcha, captcha_id

```
上面定义了一个获取验证码的函数。原理就是，先获取登陆页面，用bs4包把验证码图片爬取到本地，在我们登陆的时候把代码敲进去就行了。

#### 构造登陆函数

```python
def login():
    captcha, captcha_id = get_captcha()
    # 增加表数据
    datas['captcha-solution'] = captcha
    datas['captcha-id'] = captcha_id
    login_page = session.post(url, data=datas, headers=headers)
    page = login_page.text
    soup = BeautifulSoup(page, "html.parser")
    result = soup.findAll('div', attrs={'class': 'title'})
    #进入豆瓣登陆后页面，打印热门内容
    for item in result:
        print(item.find('a').get_text())
# 运行程序
login()

#please input the captcha:under
#芳华绝代，偿命百岁
#中国的，太中国的
#精简护肤| 消肿提亮，还你一张明艳动人出差脸。
#杭 州
#2017年度观影总结 及 私人观影二十五佳作
#我假装羞愧，只是为了融入当代的生活
#现在，在海拉尔 ——《荒野之息》中的后现代主题
#冬至饺子，岂能少了天赐的荠菜？
#某些星星
#去瑞士坐一趟开往幸福的列车
#谁的金缕玉衣身材最好？ | 满城汉墓1
#死亡-灵魂将安放何处
#群像的芳华与历史语境的含混性
#在都市里 女工都是隐形人：吕途、张慧瑜、范雨素三人谈
#开始与结束之间的一切——2017年阅读总结
#浮光掠影.2017
#黑死病、城市与文艺复兴
#如何看待《芳华》中刘峰和何小萍这两个人物？
#为什么余光中被如此之多的人纪念？
#美国人为什么一定要拿枪
```
上面构造了登陆函数，里面调用了获取验证码的过程，然后打印了首页的热门内容，最后一行是运行这个函数，输入验证码就可以登陆成功了。
模拟登陆的内容就先告一段落了，其实还有一个cookie登陆方法，cookie是保存登陆信息的一个文件，包括账号密码等信息，通过它可以直接登陆，不过这个方法我不太会。

#### 查看登陆状态
```python
def isLogin():
    '''
    通过查看用户个人账户信息来判断是否已经登录
    '''
    url = "https://www.douban.com/accounts/"
    login_code = session.get(url, headers=headers,
                             allow_redirects=False).status_code
    if login_code == 200:
        return True
    else:
        return False
isLogin()
#True
```
还是用大招吧，虽然之前已经打印了热门内容了，用这个方法可以直接重个人信息里查看是否登陆成功。

## 避开反爬虫
防止自己被反爬虫识别的要领就是让我们的采集行为看起来像一个机器人,下面介绍我常用的两个小操作。

### 设置标头-header
首先看一看上面我们登陆豆瓣的请求信息

|  属性          | 内容         |
| ------------- |:-------------:| 
| Host|www.douban.com|
| Referer|    https://www.douban.com/|
| User--Agent| Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0|
| Accept| text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8|
| Accept--Language|  zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3|
| Accept--Encoding | gzip, deflate, br|
再看看用python的urllib库访问网站的时候，发送的请求信息。怎么样，是不是把“我是机器人”这几个大字写在脸上了，再简陋额反扒机制也能轻轻松松认出你。

|  属性          | 内容         |
| ------------- |:-------------:| 
| Accept-Encoding | identity |
| User-Agent| Python-urllib/3.6 |

浏览器的访问信息可以在浏览器里用F12打开开发者工具在下图的位置查看，关键的内容是User-Agent部分，不要有一个可疑的身份。
![pic10](https://raw.githubusercontent.com/XufengHan/Month-Cost/master/pic/header1.png)
### 执行间隔
除了身份要伪造以外，行为也要伪装，你的访问速度比别人快很多不光会显得很异常，而且会给对方的服务器造成沉重的负担，解决方案就是为每一次访问增加一些时间间隔，用time.sleep()函数，就能完成，下面有一个使用实例。
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import random as rd
import time
url="https://www.douban.com/tag/2017/movie?start="

movies=[]
for i in range(1,20):
  time.sleep(rd.uniform(5, 10))# 随机休眠5到10秒  
  url="https://www.douban.com/tag/2017/movie?start="
  url1=url+"15*i"
  html = urlopen(url1)
  douban = BeautifulSoup(html,"lxml")
  nameList = douban.findAll("a", {"class":"title"})
  for i in range(0,15):
    nameList[i]=nameList[i].get_text()
  movies=movies+nameList #储存数据
```
这几行代码用了一个循环，把豆瓣电影前20页的电影名称拉下来了，并且每一次翻页都暂停了5到10秒。
避免反爬虫的方法就暂时介绍到这里，当然爬虫者和反爬者还有很多勾心斗角的斗争，比如围绕着cookie和IP地址的，但是这些在我的能力之外了，不过我的方案有着绝对的性价比，够解决我们遇到的大多数问题了。

## 验证码识别
可能有人有疑惑，上面不是已经有了处理验证码的方案了吗，为什么还要去做自动识别呢？因为我们头铁，怎么会满足这么笨重的方式。
这里我打算做两个关于图像识别的模型，主要基于下面两个方法。
-[pillow识别验证码](http://www.hanxufeng.club/2017/12/27/pillow-pic/)
-[tensorflow识别验证码](http://www.hanxufeng.club/2017/12/11/tensorflow-rumen/)
当然了，验证码不一定都是文字和数字的组合，现在很流行的滑动验证码我也看到过破解方式，是调用JS脚本做的。至于规则更为复杂的验证码比如下面这样的，我也不太有办法了。
![pic1](https://raw.githubusercontent.com/XufengHan/Month-Cost/master/pic/nmcx.jpg)
 

