## scrapy上手

**改爬虫规则**

```
###settings.py###
# Obey robots.txt rules
ROBOTSTXT_OBEY = False
```

---

### spiders=>新建爬虫pp01.py

```
import scrapy
class Pp01Spider(scrapy.Spider):
	# 名字很重要，运行爬虫是以该名字运行，而非py文件名
    name = 'pp01'
    # 先使用httpbin.org进行请求、响应内容检查
    # 需要注意allowed_domains是限制下面爬取网址的参数，如果发现没有爬取结果又不报错的话，那么可能是你在接下来的多次爬取Request请求中有域名不在这个列表中，需要添加进去。
  	# start_urls是开始爬的网站url地址，这里需要以http://开头，注意填写完整，否则会出现爬不全的现象。
    allowed_domains = ['httpbin.org']
    start_urls = ['http://www.httpbin.org/get']
	# 重写start_requests方法，配置必要参数url和callback
    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.Request(url=url, callback=self.parse)
	# 前面重写的start_requsets不重写时默认会走parse方法，建议最好写上
    def parse(self, response):
        print(response.text)
        print("I'm default parse")
        pass
```

---

#### IDE执行爬虫

如果需要爬虫在pycharm端运行并在终端显示结果的话，需要新建一个run.py文件

```
该代码右键运行后会模拟cmd端运行scrapy crawl pp01 --nolog 命令执行爬虫(--nolog取消显示日志打印)
from scrapy.cmdline import execute
execute(['scrapy', 'crawl', 'pp01', '--nolog'])

如果输出出现乱码可以添加代码解决
import sys,os
sys.stdout=io.TextIOWrapper(sys.stdout.buffer,encoding='gb18030')
```

---

#### 修改请求头

向httpbin.org发送get请求查看响应文本可以发现：

```
"User-Agent": "Scrapy/1.7.3 (+https://scrapy.org)"
```

改之

```
###settings.py###
# Override the default request headers:
DEFAULT_REQUEST_HEADERS = {
  'Accept': 		'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
  'Accept-Language': 'en',
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36',
}
```

#### 在每个爬虫中单独配置settings

```
###Pp01Spider类中###
custom_settings = {
        'DEFAULT_REQUEST_HEADERS': {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36'
        }
    }
```

查找顺序：先找自己的custom_settings=>settings.py

---

#### 使用scrapy爬取天猫商城数据

##### 分析搜索商品结果分页url

1.搜索显卡，可以知道商城搜索显示的第一页是默认可以访问的，如果点下面的分页标签会跳转到登录页面。

2.然后发现分页标签右边有页面跳转项，于是输入2，点击跳转同样还是一个登录页面，但是这个登录页面有点不一样，后面重定向的url有变化

```
https://login.taobao.com/member/login.jhtml?redirectURL=http://list.tmall.com/search_product.htm?type=pc&q=%CF%D4%BF%A8&totalPage=80&sort=s&style=g&from=mallfp..pc_1_suggest&suggest=0_1&jumpto=2
```

分析过程：

```
商品展示页面基础url应该是：
http://list.tmall.com/search_product.htm?type=pc&q=%CF%D4%BF%A8
这里试着把type=pc去掉，发现一样，所有
http://list.tmall.com/search_product.htm?q=%CF%D4%BF%A8
这个url就是我们的start_urls
继续分析：
totalPage=80 总页数
jumpto=2	跳转页
然后通过不断试验，发现如果去掉sort=s，发现使用
http://list.tmall.com/search_product.htm?q=%CF%D4%BF%A8&totalPage=80&style=g&from=mallfp..pc_1_suggest&suggest=0_1&jumpto=2
就能不登录进入第二页，紧接着继续去掉没用的参数，最后得出第二页的url
http://list.tmall.com/search_product.htm?q=%CF%D4%BF%A8&totalPage=80&jumpto=2
将jumpto=2改成jumpto=1，发现跳到第1页，且不出现登录页面
将jumpto=1改成jumpto=10，发现跳到第10页，且不出现登录页面
天猫搜索商品页面url分析完成
```

分析结果：

```
start_urls = ['https://list.tmall.com/search_product.htm?q=%CF%D4%BF%A8']
后面继续拼接以下2个参数代表第n页
totalPage=80 总页数
jumpto=2	跳转页
```

##### 获取总页数，拼接每一页url，循环发送请求

**在spiders/pp01.py中进行**

配置

```

class Pp01Spider(scrapy.Spider):
    name = 'pp01'
    allowed_domains = ['tmall.com', 'taobao.com']
    start_urls = ['https://list.tmall.com/search_product.htm?q=%CF%D4%BF%A8']

    custom_settings = {
        'DEFAULT_REQUEST_HEADERS': {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36'
        }
    }
```

拿到数据

```
# 发送请求，通过callback=self.totalPage_parse回调，将请求的响应交由totalPage_parse方法分析
def start_requests(self):
	for url in self.start_urls:
	yield scrapy.Request(url, callback=self.totalPage_parse, dont_filter=True)
	
# 得到响应，分析响应
def totalPage_parse(self, response):
    totalPage = response.css('[name="totalPage"]::attr(value)').extract_first()
    url = self.start_urls[0] + '&totalPage=%s&jumpto={}' % totalPage
    # 这里先爬2页数据进行代码测试，无问题后再进行全部爬取
    # for i in range(1, int(totalPage)+1):
    for i in range(1, 3):
        jump_url = url.format(i)
        yield scrapy.Request(jump_url, callback=self.info_parse)
        
# 上面的总页数得到后，开始for循环拼接每一页url然后再返回requests，通过回调再次拿到响应对响应分析获取商品相关数据。
def info_parse(self, response):
    info_selectors = response.css('[class="product  "]')
    for info_selector in info_selectors:
        img_url = info_selector.css('.productImg-wrap img::attr(src)').extract_first()
        if not img_url:
            img_url = info_selector.css('.productImg-wrap img::attr(data-ks-lazyload)').extract_first()
        img_url = 'http:%s' % img_url
        title = info_selector.css('.productTitle a::attr(title)').extract_first()
        price = info_selector.css('.productPrice em::attr(title)').extract_first()
        shop_name = info_selector.css('.productShop a::text').extract_first().strip('\n')
        shop_t_url = 'http:' + info_selector.css('.productShop a::attr(href)').extract_first()
        实例化出item对象，作为保存爬取到的数据的容器
        item = items.Demo0Item()
        item['img_url'] = img_url
        item['title'] = title
        item['price'] = price
        item['shop_name'] = shop_name
        # 由于店铺的url地址需要进一步请求shop_t_url才能拿到，所以我们还需要返回一次request，同时该方法内获取到的数据在item中使用meta参数携带过去。
        yield scrapy.Request(shop_t_url, meta={'item': item}, callback=self.shopIndex_parse, dont_filter=True)

#通过上面携带的meta参数拿到已经获取到部分数据的item容器，然后再通过请求url再次在响应中获取到店铺的url，存入item中，自此所有需要数据获取完成，返回item
def shopIndex_parse(self, response):
    print('==' * 50)
    item = response.meta['item']
    shop_url = 'http:' + response.css('.slogo-shopname::attr(href)').extract_first()
    item['shop_url'] = shop_url
    yield item
```

**在items.py和pipelines.py中进行**

数据持久化

```
分析：
.我们获取网站的数据，并且把这些数据保存到Item容器，最后需要通过pipelines把数据存放到数据库中去
.第一步：把这个Item yield出去，yield出去之后，这个Item就会进入到pipelines里面去。
.第二步：在pipelines编写一个接收Item的class，编写一个process_item（名字是固定的）的方法（带有item参数），同时在settings.py把这个class配置上。
ITEM_PIPELINES = {
   'demo0.pipelines.Demo0Pipeline': 300,
}

```

item建模(定义持久化数据的字段)

```
# items.py
import scrapy
class Demo0Item(scrapy.Item):
	# 注意，下面的所有变量都会对应存入数据库中的key值，且，变量要和签名获取数据时通过字典形式存入的key值一样，否则不会报错但item会出问题。
    img_url = scrapy.Field()
    title = scrapy.Field()
    price = scrapy.Field()
    shop_name = scrapy.Field()
    shop_url = scrapy.Field()
```

使用mongodb进行存储数据，需要在settings.py文件中配置数据库参数

```
MONGO_DB_DATA = {
  'host': '127.0.0.1',
  'port': 27017,
  'username': 'root',
  'password': '123'
}

MONGO_DB = 'spider_tmall'
MONGO_DB_TABLE = 'spider_tmallcards'
```

```
import pymongo
from .settings import MONGO_DB_DATA, MONGO_DB, MONGO_DB_TABLE
class Demo0Pipeline(object):
    # 爬虫开始前执行该方法，可用于打开数据库
    def open_spider(self, spider):
        self.client = pymongo.MongoClient(**MONGO_DB_DATA)
        print(MONGO_DB, MONGO_DB_TABLE)
        # 切换数据库并得到表对象
        self.table = self.client[MONGO_DB][MONGO_DB_TABLE]
        print('开始')

    def process_item(self, item, spider):
        self.table.insert(dict(item))
        print('写入数据库...')
        return item

    # 爬虫结束后执行该方法，可用于关闭数据库
    def close_spider(self, spider):
        self.client.close()
        print('结束')
```

