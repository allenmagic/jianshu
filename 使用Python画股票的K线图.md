
## 导言

> 本文简单介绍了如何从网易财经获取某支股票的价格数据，并根据价格数据画出相应的
> 日K线图。有助于新手了解并使用Python的相关功能。包括列表、自定义函数、for循
> 环、if函数以及如何使用matplotlib进行作图等内容。


## 第一步：从网易财经获取股票的价格数据

我一般是在网易财经查看某支股票的价格和成交数据，[网易财经](http://money.163.com/)可以查到任意沪深的股票，我们使用[招商银行](http://quotes.money.163.com/trade/lsjysj_600036.html#06f01)的数据作为参考。

### 1、构建爬虫获取股票价格数据
这里不对Python做介绍了，如果需要了解什么是Python，可以自行百度或者访问[Python官网](https://www.python.org/).

**加载需要的模块**

代码如下：

```python
import re,urllib2,time,csv,datetime
import matplotlib as mpl
import matplotlib.pyplot as plt
import matplotlib.finance as mpf
import matplotlib.dates as mpd
```
其中urllib2是用来解析HTML内容的包，主要是从url获取网页内容；re是正则表达式包，本文会使用正则表达式来从抓取的网页数据中获取到有用的数据；time和datetime是时间相关的包，主要用来设定要抓取的时间以及其它相关时间的处理；csv包是用来生成csv数据（该数据会被用于R来画K线图），其余的几个包会在使用时单独介绍，你也可以在需要的时候在程序头部补充import。

**设定时间相关**

代码如下：


```python
t = time.localtime()  # 获取当前的本地时间
year = range(t[0],1989,-1)  # 设定年度范围，从当前年度至沪市开市的年份倒序生成
season = range(4,0,-1)    # 生成季度的数据列表，从4季度到1季度倒序生成
```
为什么要这么设定时间呢？仔细的查看网易股票数据的url，是按照年度和季度来构成的，我们发现搜索数据也是用年度和季度来搜索的。
![招商银行2017年1季度数据](http://7viihf.com1.z0.glb.clouddn.com/600036.png)
其url构成如下：*http://quotes.money.163.com/trade/lsjysj_600036.html?year=2017&season=1*可见可拆为6个子字符串，分别是*http://quotes.money.163.com/trade/lsjysj_*、*600036*、*.html?year=*、*2017*、*&season=*、*1*。其中第2、4、6个子串可以参数化输入获取特定需求的数据。

**定义获取数据的函数**

代码如下：


```python
def getData(url):
	request = urllib2.Request(url)
	response = urllib2.urlopen(request)
	content = response.read()

	pattern = re.compile('</thead[\s\S]*</tr>    </table>')
	ta = re.findall(pattern, str(content))
	pattern1 = re.compile("<td class='cGreen'>")
	pattern2 = re.compile("<td class='cRed'>")
	pattern3 = re.compile(",")
	tab1 = re.sub(pattern1,"<td>",str(ta))
	tab2 = re.sub(pattern2,"<td>",str(tab1))
	tab  = re.sub(pattern3, "", str(tab2))

	if len(tab) == 0:
		data = []
	else:
		pattern3 = re.compile('<td>(.*?)</td>')
		data = re.findall(pattern3, str(tab))

	for d in data:
		if d == '':
			data.remove('')

	return data
```
本段代码定义个一个函数getDate(url)，函数名为getData，参数为url。相当于从该url获取股票的交易数据，显然这个函数是定制的。

首先，我们用urllib2模块的相关函数解析并获取网页的数据。第二步，使用re模块的数据对抓取的网页内容进行初步的处理，分为了三个过程
1. 首先匹配"</thead[\s\S]*</tr>    </table>"之间的内容并返回，因为在这之间的内容包含了所有需要的数据，这是一个简单的正则表达式，表示返回</thead和</tr>    </table>两个字符串之间的所有内容
2. 匹配<td class='cGreen'>、<td class='cRed'>并使用<td>替换，因为这两个字符串会影响后续的匹配数据，现行替换掉可以更方便的匹配到需要的数据
3. 替换到千分位","号，因为Python和R并不会识别有千分位号的数据，所以我们要将数据转换为非千分位的数据。
4. tab是按照要求最后获取的包含数据和文本的原始内容
5. 用if函数来获取除文本的数据，因为如果year和season超过了当前的界限，会返回空的tab，所以我们在这里进行判断，如果少了这个判断，会报出index error。这个if函数表示了如果tab为空，data也是个空的列表，如果tab不为空，那么根据pattern3返回需要的数据至data列表
6. 用一个for循环来遍历data列表，删除空白的内容（其实这一步不需要，因为在if中已经剔除了空的内容。

所以定义了以上的函数后，就可以使用该函数返回特定url的数据。

**获取某支股票的数据**

代码如下：

```python
def get_stock_price(code):
	url1 = "http://quotes.money.163.com/trade/lsjysj_"
	url2 = ".html?year="
	url3 = "&season="
	urllist = []
	for k in year:
		for v in season:
			urllist.append(url1+str(code)+url2+str(k)+url3+str(v))
	
	price = []
	for url in urllist:
		price.extend(getData(url))
	return price
```
自定义get_stock_price(code)函数，code是指股票代码，使用该函数可以返回该股票所有的历史数据（OHLC以及其它）思路很简单：
1. 根据code构建其股票数据的页面的url列表
2. 使用getData(url）函数和for循环，返回所有的历史数据

最终返回的是price的数据列表

这样，我们就可以使用该函数获取某支股票的所有历史数据：

```python
# get all histrocial data include all price and others
price = get_stock_price(600036)
```
获取招商银行（600036）的所有历史数据。

### 2、保存数据

**保存为csv文件**

代码如下：

```python
writer = csv.writer(file("stock.csv",'wb'))
writer.writerow(['Date','Open','High','Low','Close','Volume'])
pr = []
for i in range(0,len(price),11):
	pr.extend([[price[i],price[i+1],price[i+2],price[i+3],price[i+4],price[i+8]]])

for prl in pr:
	writer.writerow(prl)
```
我们使用csv模块保存数据为csv文件，用于在R中读取并作图，我们查看在网易的数据展示可以发现，总共11个字段，所有我们在每11个切片中，返回时间、OHLC（开盘价、最高价、最低价、收盘价）和交易量的数据并保存为csv的文件格式。

**处理保存数据到列表**

代码如下：

```python
# get the number for date by date2num
def Date_no(strdate):
	t = time.strptime(strdate, "%Y-%m-%d")
	y,m,d = t[0:3]
	d = datetime.date(y, m, d)
	n = mpd.date2num(d)

	return n

# get the price data 
pr = []
for i in range(0,len(price),11):
	pr.extend([[
		Date_no(price[i])
		,float(price[i+1])
		,float(price[i+2])
		,float(price[i+3])
		,float(price[i+4])
		,float(price[i+8])]]
		)
```
这个程序片段是用来处理和保存数据用于在pyhton中做出K线图。
1. 定义函数将字符串的时间处理为matplotlib中作图使用的数值（直接获取的数据中时间是字符串）
2. 返回返回时间、OHLC（开盘价、最高价、最低价、收盘价）和交易量的数据并存储在pr这个列表里

## 第二步：做出K线图

**在R中作图**

代码如下：

```r
library(quantmod)

rm(list = ls())
setwd("~/GitHub/index/")
price <- as.xts(read.zoo("stock.csv",header=TRUE,sep=",",colClasses = c("Date", rep("numeric",5))))

n <- nrow(price)
m <- nrow(price)-100

#pdf(file = "k.pdf")
chartSeries(price[c(m:n)],theme = chartTheme("white"),up.col = "red",dn.col = "green",name = "600036",time.scale = 0.5,line.type = "l",bar.type = "ohlc",major.ticks='auto', minor.ticks=TRUE)
#dev.off()

```
做出的图片效果如下：
![](http://7viihf.com1.z0.glb.clouddn.com/Rplot01.png)
R中可以使用quantmod包中的chartSeries函数画出K线图，具体的使用方法可以参考[chartSeries参考文档](http://www.quantmod.com/documentation/chartSeries.html)

**在Python中使用matplotlib作图**

代码如下：

```python
quotes = pr[0:80]

print(quotes)

fig,ax = plt.subplots(figsize=(30,6))
fig.subplots_adjust(bottom=0.2)
mpf.candlestick_ohlc(ax,quotes,width=0.4,colorup='r',colordown='g')
plt.grid(False)
ax.xaxis_date()
ax.autoscale_view()
plt.setp(plt.gca().get_xticklabels(), rotation=30) 
plt.show()
```
K线效果图如下：
![](http://7viihf.com1.z0.glb.clouddn.com/figure_1.png)
使用matplotlib的candlestick_ohlc的[参考文档](http://matplotlib.org/api/finance_api.html?highlight=candlestick#matplotlib.finance.candlestick2_ohlc),但是目前有一些问题，比如会将非交易日期也置放在x轴，会到至K线出现断裂，等待下一步的解决方法吧。

相关的代码已经同步到最大的同性交友网站[我的Github](https://github.com/allenmagic/index)上了，可以参考，其中[stock.py](https://github.com/allenmagic/index/blob/master/stock.py)是主要程序。


>写在最后：因为我有近5年没使用过python了，所有代码可能不太简练。我也旨在解决问题，当然解决问题的方法千万种，比如这个例子，最直接的办法就是使用网易的下载所有（或者特定时间段）的数据为csv格式，然后用Excel画K线也可以的。

