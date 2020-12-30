

![image-20200523124133395](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200523124133395.png)

```
import datetime,json
def parse():
    # 下面数据涵盖了python的各种类型
    # python==>json
    d={
        'name':'Python书籍',
        'sale_price':55.3,
        'origin_price':66,
        'pub_date':datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'),
        'store':['京东','淘宝'],
        'author':('jom','jack','zare'),
        'is_Valid':True,
        'is_sale':False,
        'meta':{
            'isbn':'abc-123',
            'pages':300
        },
        'desc':None
    }
    rest=json.dumps(d,indent=4)
    print(rest)
def json_to_python():
     data='''
    {"author":["jack","rom"],"meta":{"isbn":"abc-123","pages":300},"name":"Python书       籍","origin_price":66,"pub_date":"2018-4-14 17:00:00","sale":false,"store":["京东","淘宝"],"valid":true}
     '''
     res=json.loads(data)
     print(data)

def json_to_python_from_file():
    # r为只读模式
    f=open('./static/book.txt','r',encoding='utf-8')
    s=f.read()
    print(s)
    f.close()

if __name__ == '__main__':
        # parse()
        json_to_python()
        # json_to_python_from_file()
```

# Xpath

## 安装

```
pip install lxml
```

## 基础的操作

```
from lxml import html
def parse():
    #将html文件使用xpath解析
    #读取文件内容
  f=open('./static/test.html','r',encoding='utf-8')
  s=f.read()
  #   解析h3标题
  selector=html.fromstring(s)
  #   单斜杠表示从指定的标签，双斜杠表示从根目录
  h3=selector.xpath('/html/body/h3/text()')
  print(h3)
    #['标题']
  ul=selector.xpath('//li/text()')
  print(ul)
    #解析ul下指定的li的值 @操作属性
  ul2=selector.xpath('//li[@class="important"]/text()')
  print(ul2)
  a_href=selector.xpath('//a/@href')
  a_text=selector.xpath('//a/text()')
  p=selector.xpath('//p[last()]/text()')
  print(p)
  print(a_href)
  print(a_text)
  f.close()

if __name__ == '__main__':
  parse()
```

## 直接在浏览器里copy xpath的表达式

```
from lxml import html
def parse():
    #将html文件使用xpath解析
    #读取文件内容
  f=open('./static/test.html','r',encoding='utf-8')
  s=f.read()
  #   解析h3标题
  selector=html.fromstring(s)
  #   单斜杠表示从指定的标签，双斜杠表示从根目录
  h3=selector.xpath('/html/body/h3/text()')
  print(h3)
    #['标题']
  ul=selector.xpath('//li/text()')
  print(ul)
    #解析ul下指定的li的值 @操作属性
  ul2=selector.xpath('//li[@class="important"]/text()')
  print(ul2)
  a_href=selector.xpath('//a/@href')
  a_text=selector.xpath('//a/text()')
  p=selector.xpath('//p[last()]/text()')
  print(p)
  print(a_href)
  print(a_text)
  f.close()

if __name__ == '__main__':
  parse()
```

# Requests库基础知识

## 安装

```
pip install requests
```

## 基础操作

```
import requests
def get_book():
    # 获取书本的信息
    url='http://e.dangdang.com/products/1900450772.html'
    rest=requests.get(url)
    print(rest.text)
    print(rest.raw)
    print(rest.encoding)


if __name__ == '__main__':
    get_book()
```

# 字符串操作

```
def format_str():
    #格式化字符串
   name='zd'
   print('在 %s' % name)
   # """利用位置方式传递"""  a,b,a
   print("{0},{1},{0}".format('a','b'))
    #使用名称
   d={
        'username':'jack',
        'num':45
    }
   # print('{username},{num}'.format(username='jack',num=12))
   print('{username},{num}'.format(**d))
   #  使用的原组
   point=(1,5)
   print('{0[1]}'.format(point))

if __name__ == '__main__':
    format_str()
```

# 爬取当当网的数据

```
import requests
from lxml import html
def spider():
    url='http://search.dangdang.com/?key=9787115428028&act=input'
    html_data=requests.get(url).text
    selector=html.fromstring(html_data)
    ul_list=selector.xpath('//div[@id="search_nature_rg"]/ul/li')
    print(len(ul_list))
    for li in ul_list:
        #标题
       title=li.xpath('a/@title')
       link=li.xpath('a/@href')
       price=li.xpath('p[@class="price"]/span[@class="search_now_price"]/text()')
       #  商家
       store=li.xpath('p[@class="search_shangjia"]/a/text()')
       print(link[0])
       print(title[0])
       print(str(price[0]).replace('¥',''))
       print('当当自营' if store==[] else store[0])


    # print(html_data)


if __name__ == '__main__':
    spider()
```

# 爬取京东网的数据

```
from lxml import html
import requests
def spider():
    # 爬取京东的图书数据
  url='https://channel.jd.com/1713-3258.html'
  html_data=requests.get(url).text
  selector=html.fromstring(html_data)
  ul_list=selector.xpath('/html/body/div[6]/div/div[2]/div[1]/div/ul[1]/li')
  print(len(ul_list))
  # print(html_data)
  for li in ul_list:
      title=li.xpath('div[@class="p-name"]/a/@title')
      link=li.xpath('div[@class="p-name"]/a/@href')
      price=li.xpath('div[@class="p-price"]/strong/text()')
      print(title[0])
      print(link[0])
      print(price)



if __name__ == '__main__':
    spider()
```

## 比较工具开发

```
from spider_dangdang import spider as dangdang
from spider_jingdong import spider as jingdong
def sort():
   # 图书价格整合工具
   book_list=[]
   #当当
   print('当当网数据爬取完成')
   dangdang(book_list)
   #某东
   # print('京东网数据爬取完成')
   # jingdong(book_list)
   #打印所有的数据
   for book in book_list:
       print(book)
   print('++++++++++++++++++')
   print('开始排序++++++++++++++++')
   book_list=sorted(book_list,key=lambda item:item["price"] ,reverse=True)
   for book in book_list:
       print(book)


if __name__ == '__main__':
       sort()
```

