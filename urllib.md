# **urllib**

所谓网页抓取，就是把URL地址中指定的网络资源从网络流中抓取出来。

在Python中有很多库可以用来抓取网页，在python2中有urllib和urllib2两个库来实现请求的发送，但是在

python3中已经不存在urllib2了，统一为urllib，官方文档链接为：<https://docs.python.org/3/library/urllib.html>

Urllib是python内置的HTTP请求库，也就是不需要额外去安装了可以直接使用，他包含了以下4个模块：
- urllib.request ：请求模块

- urllib.error ：异常处理模块

- urllib.parse  ：url解析模块

- urllib.robotparser ：robots.txt解析模块

 

## urllib的基础使用

### 1) **urlopen**

关于urllib.request.urlopen参数的介绍：

```
urllib.request.urlopen(url, data=None, [timeout, ]*,cafile=None, capath=None, cadefault=False, context=None)
```

先写一个简单的例子：

```python
import urllib.request
response = urllib.request.urlopen('http://www.baidu.com') print(response.read().decode('utf-8'))
```

实际上，如果我们在浏览器上打开百度主⻚， 右键选择“查看源代码”，你会发现，跟我们刚才打印出来的是⼀模⼀样。也就是说，上⾯的3⾏代码就已经帮我们把百度的⾸⻚的全部代码爬了下来。

response.read()可以获取到网页的内容，如果没有read()，将返回如下内容

```
<http.client.HTTPResponse object at 0x7ff84809f550>
```

#### **data参数**

上面的例子是通过请求百度的get请求获得百度，下面使用urllib的post请求，

这里通过[http://httpbin.org/post ](http://httpbin.org/post)网站演示（该网站可以作为练习使用urllib的一个站点使用，可以模拟各种请求操作）。

**httpbin这个网站能测试 HTTP 请求和响应的各种信息，比如 cookie、ip、headers 和登录验证等，且支持 GET、POST 等多种方法，对 web 开发和测试很有帮助。它用 Python + Flask 编写，是一个开源项目。**

```python
import urllib.parse import urllib.request

data = bytes(urllib.parse.urlencode({'word': 'python'}), encoding='utf8') print(data)

response = urllib.request.urlopen('http://httpbin.org/post', data=data) print(response.read())
```

这里就用到urllib.parse，通过bytes(urllib.parse.urlencode())可以将post数据进行转换放到

urllib.request.urlopen的data参数中。这样就完成了一次post请求。   所以如果我们添加data参数的时候就是**以post请求方式请求，如果没有data参数就是get请求方式**

GET和POST的区别？

- GET方式是直接以链接形式访问，链接中包含了所有的参数，服务器端用Request.QueryString获取变量的值。如果包含了密码的话是一种不安全的选择，不过你可以直观地看到自己提交了什么内容。

- POST则不会在网址上显示所有的参数，服务器端用Request.Form获取提交的数据，在Form提交的时候。但是HTML代码里如果不指定 method 属性，则默认为GET请求，Form中提交的数据将会附加在url之后，以?分开与url分开。

- 表单数据可以作为 URL 字段（method="get"）或者 HTTP POST （method="post"）的方式来发送。比如在下面的HTML代码中，表单数据将因为 （method="get"） 而附加到 URL 上：

#### timeout

在某些网络情况不好或者服务器端异常的情况会出现请求慢的情况，或者请求异常，所以这个时候我们需要给请求设置一个超时时间，而不是让程序一直在等待结果。

```python
import urllib.request
response = urllib.request.urlopen('http://httpbin.org/get', timeout=1) print(response.read())
```

#### request

在上一个例子里，urlopen()的参数就是一个url地址；

但是如果需要执行更复杂的操作，比如有很多网站为了防止程序爬虫爬网站造成网站瘫痪，会需要我们携带一些headers头部信息才能访问，必须创建一个 Request 实例来作为urlopen()的参数；而需要访问的url地址则作为

Request 实例的参数。

```python
import urllib.request

## url 作为Request()方法的参数，构造并返回一个Request对象
request = urllib.request.Request('https://www.baidu.com')

## Request对象作为urlopen()方法的参数，发送给服务器并接收响应 response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))
```

上面我们说了，有很多网站不喜欢被程序（非人为访问）访问，为了防止程序爬虫爬网站造成网站瘫痪，网站会限制需要我们携带一些headers头部信息才能访问，打个很形象的例子，我和你两个人，你能直接进你家，但是我直接进你家的话就会被你爸拦下来，因为你是你爸的儿子而我不是，所以要进你家的话我需要一个身份，你和你爸说我是你朋友来你家玩，这样你爸肯定让我进了，这个身份就是所谓的User-Agent头，你带上了这个就会被允许访问。

```python

import urllib.request

url = 'https://www.baidu.com'

User_Agent = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.139 Safari/537.36'}

## url 和 User_Agent 作为Request()方法的参数，构造并返回一个Request对象
request = urllib.request.Request(url=url,header=User_Agent)

## Request对象作为urlopen()方法的参数，发送给服务器并接收响应 
response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))
```

添加请求头的第二种方式,添加更多的Header信息,这种添加方式有个好处是自己可以定义一个请求头字典，然后循环进行添加

```python

import urllib.request

url = 'https://www.baidu.com'

User_Agent = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.139 Safari/537.36'}

request = urllib.request.Request(url=url,header=User_Agent) request.add_header("Connection",  "keep-alive")
#  也可以通过调用Request.get_header()来查看header信息
# request.get_header(header_name="Connection") 
response = urllib.request.urlopen(request)
print response.code	#可以查看响应状态码

print(response.read().decode('utf-8'))
```

### 2) **URL**解析

**urlencode**

这个方法可以将字典转换为url参数，

```python

import urllib.request, urllib.parse

word = {"wd": "python"}

# 通过urlencode将字典键值对按URL编码转换，从而能被web服务器接受。 
word_encode = urllib.parse.urlencode(word) 
print(word_encode)

# 通过unquote把 URL编码字符串，转换回原先字符串。 
word_str = urllib.parse.unquote(word_encode) 
print(word_str)
```

一般HTTP请求提交数据，需要编码成     URL编码格式，然后做为url的一部分，或者作为参数传到Request对象中

**urlparse**

```python
urllib.parse.urlparse(urlstring, scheme='', allow_fragments=True)
```

该方法可以实现URl的识别和分段，看下面实例：

```python
from urllib.parse import urlparse

result = urlparse("https://www.baidu.com/s?wd=python") 
print(result)


ParseResult(scheme='https', netloc='www.baidu.com', path='/s', params='', query='wd=python', fragment='')
```

将url分为6个部分，返回一个包含6个字符串项目的元组：协议、位置、路径、参数、查询、片段。其中scheme 是协议, netloc 是域名服务器,path 相对路径 ,params是参数，query是查询的条件

**urlunpars**

其实功能和urlparse的功能相反，它是用于拼接，它接受的参数是一个可迭代对象，但是它的长度必须是6，否则会抛出参数数量不足或者过多的问题。

```python
from urllib.parse import urlunparse

data = ['http','www.baidu.com','index.html','user','a=123','commit'] print(urlunparse(data))

http://www.baidu.com/index.html;user?a=123#commit
```

### 3) **异常处理**

在很多时候我们用urlopen或opener.open方法发出一个请求访问页面时，如果urlopen或opener.open不能处理这个response，页面就会产生错误,比如404，500等

这里主要说的是URLError和HTTPError，以及对它们的错误处理。



#### **URLError**

URLError  产生的原因主要有：

1. 没有网络连接

2. 服务器连接失败

3. 找不到指定的服务器

我们可以用try      except语句来捕获相应的异常。下面的例子里我们访问了一个不存在的域名：

```python
from urllib import request,error

try:
	response = request.urlopen("http://pythonsite.com/1111.html") 
except error.URLError as e:
	print(e.reason)
```

结果：

Not Found

URLError里只有一个属性：reason,即抓异常的时候只能打印错误信息，类似刚才的例子



#### **HTTPError**

HTTPError是URLError的子类，我们发出一个请求时，服务器上都会对应一个response应答对象，其中它包含一个数字"响应状态码"。

如果urlopen或opener.open不能处理的，会产生一个HTTPError，对应相应的状态码，HTTP状态码表示HTTP协议所返回的响应的状态。

注意，urllib2可以为我们处理重定向的页面（也就是**3**开头的响应码），**100-299**范围的号码表示成功，所以我们只能看到**400-599**的错误号码。

```python

from urllib import request, error

try:
	response = request.urlopen("http://pythonsite.com/1111.html") 
except error.HTTPError as e:
	print(e.reason) 
    print(e.code) 
    print(e.headers)
except error.URLError as e: 
    print(e.reason)

else:
	print("reqeust successfully")
```



```
Not Found 404
Date: Thu, 10 May 2018 02:05:36 GMT
Server: Apache
Vary: Accept-Encoding Content-Length: 207 Connection: close
Content-Type: text/html; charset=iso-8859-1
```

同时，e.reason其实也可以在做深入的判断，

```python

import socket
from urllib import error,request

try:
	response = request.urlopen("http://www.pythonsite.com/",timeout=0.001) 
except error.URLError as e:
	print(type(e.reason))
    if isinstance(e.reason,socket.timeout): 
        print("time out")
```

```
<class 'socket.timeout'> 
time out
```

**HTTP**响应状态码：

```
1xx:信息

100 Continue
服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求。
101 Switching Protocols
服务器转换协议：服务器将遵从客户的请求转换到另外一种协议。
```

```
2xx:成功

200OK
请求成功（其后是对GET和POST请求的应答文档）
201 Created
请求被创建完成，同时新的资源被创建。
202 Accepted
供处理的请求已被接受，但是处理未完成。
203 Non-authoritative Information
文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝。
204 No Content
没有新文档。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而Servlet可以确定用户文档足够新，这个状态代码是很有用的。
205 Reset Content
没有新文档。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容。
206 Partial Content
客户发送了一个带有Range头的GET请求，服务器完成了它。
```

```
3xx:重定向

300 Multiple Choices
多重选择。链接列表。用户可以选择某链接到达目的地。最多允许五个地址。
301 Moved Permanently
所请求的页面已经转移至新的url。
302 Moved Temporarily
所请求的页面已经临时转移至新的url。
303 See Other
所请求的页面可在别的url下被找到。
304 Not Modified
未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。
305 Use Proxy
客户请求的文档应该通过Location头所指明的代理服务器提取。
306 Unused
此代码被用于前一版本。目前已不再使用，但是代码依然被保留。
307 Temporary Redirect
被请求的页面已经临时移至新的url。
```

```
4xx:客户端错误

400Bad Request
服务器未能理解请求。
401Unauthorized
被请求的页面需要用户名和密码。
401.1
登录失败。
401.2
服务器配置导致登录失败。

401.3
由于 ACL 对资源的限制而未获得授权。
401.4
筛选器授权失败。
401.5
ISAPI/CGI 应用程序授权失败。
401.7
访问被 Web 服务器上的 URL 授权策略拒绝。这个错误代码为 IIS 6.0 所专用。
402Payment Required
此代码尚无法使用。
403Forbidden
对被请求页面的访问被禁止。
403.1
执行访问被禁止。
403.2
读访问被禁止。
403.3
写访问被禁止。
403.4
要求 SSL。 403.5
要求  SSL 128。
403.6
IP 地址被拒绝。
403.7
要求客户端证书。
403.8
站点访问被拒绝。
403.9
用户数过多。
403.10
配置无效。
403.11
密码更改。
403.12
拒绝访问映射表。
403.13
客户端证书被吊销。
403.14
拒绝目录列表。
403.15
超出客户端访问许可。
403.16
客户端证书不受信任或无效。
403.17
客户端证书已过期或尚未生效。
403.18
在当前的应用程序池中不能执行所请求的 URL。这个错误代码为 IIS 6.0 所专用。
403.19
不能为这个应用程序池中的客户端执行 CGI。这个错误代码为 IIS 6.0 所专用。
403.20
Passport 登录失败。这个错误代码为 IIS 6.0 所专用。
404Not Found

服务器无法找到被请求的页面。
404.0
没有找到文件或目录。
404.1
无法在所请求的端口上访问 Web 站点。
404.2
Web 服务扩展锁定策略阻止本请求。
404.3
MIME 映射策略阻止本请求。
405Method Not Allowed
请求中指定的方法不被允许。
406Not Acceptable
服务器生成的响应无法被客户端所接受。
407Proxy Authentication Required
用户必须首先使用代理服务器进行验证，这样请求才会被处理。
408Request Timeout
请求超出了服务器的等待时间。
409Conflict
由于冲突，请求无法被完成。
410Gone
被请求的页面不可用。
411Length Required
"Content-Length"  未被定义。如果无此内容，服务器不会接受请求。
412Precondition Failed
请求中的前提条件被服务器评估为失败。
413Request Entity Too Large
由于所请求的实体的太大，服务器不会接受请求。
414Request-url Too Long
由于url太长，服务器不会接受请求。当post请求被转换为带有很长的查询信息的get请求时，就会发生这种情况。
415Unsupported Media Type
由于媒介类型不被支持，服务器不会接受请求。
416Requested Range Not Satisfiable
服务器不能满足客户在请求中指定的Range头。
417Expectation Failed
执行失败。
423
锁定的错误。
```

```
5xx:服务器错误

500 Internal Server Error
请求未完成。服务器遇到不可预知的情况。
500.12
应用程序正忙于在 Web 服务器上重新启动。
500.13
Web 服务器太忙。
500.15
不允许直接请求 Global.asa。 500.16
UNC 授权凭据不正确。这个错误代码为 IIS 6.0 所专用。
500.18
URL 授权存储不能打开。这个错误代码为 IIS 6.0 所专用。
500.100
内部  ASP 错误。
501 Not Implemented
请求未完成。服务器不支持所请求的功能。
502 Bad Gateway
请求未完成。服务器从上游服务器收到一个无效的响应。
502.1
CGI 应用程序超时。 · 502.2
CGI 应用程序出错。
503 Service Unavailable
请求未完成。服务器临时过载或当机。
504 Gateway Timeout
网关超时。
505 HTTP Version Not Supported
服务器不支持请求中指明的HTTP协议版本
```

## **urllib**的高级使用

### 1) **Opener**

opener是  urllib.request.OpenerDirector  的实例，我们之前一直都在使用的urlopen，它是一个特殊的

opener（也就是模块帮我们构建好的）。

但是基本的urlopen()方法不支持代理、Cookie等其他的 HTTP/HTTPS高级功能。所以要支持这些功能：使用相关的 Handler处理器 来创建特定功能的处理器对象；

然后通过 urllib.request.build_opener()方法使用这些处理器对象，创建自定义opener对象；使用自定义的opener对象，调用open()方法发送请求。

注意：如果程序里所有的请求都使用自定义的opener，可以使用urllib.request.install_opener()   将自定义的

opener 对象 定义为 全局opener，表示如果之后凡是调用urlopen，都将使用这个opener（根据自己的需求来选择）。

**简单的自定义****opener()**

```python
import urllib.request

# 构建一个HTTPHandler 处理器对象，支持处理HTTP请求
http_handler = urllib.request.HTTPHandler()

#   调用urllib.request.build_opener()方法，创建支持处理HTTP请求的opener对象
opener = urllib.request.build_opener(http_handler)

# 构建 Request请求
request = urllib.request.Request("http://www.baidu.com/")
#  调用自定义opener对象的open()方法，发送request请求
#  （注意区别：不再通过urllib.request.urlopen()发送请求）
response = opener.open(request)

# 获取服务器响应内容
print(response.read())
```

这种方式发送请求得到的结果，和使用urllib.request.urlopen()发送HTTP/HTTPS请求得到的结果是一样的。

如果在 HTTPHandler()增加 debuglevel=1参数，还会将 Debug Log 打开，这样程序在执行的时候，会把收包和发包的报头在屏幕上自动打印出来，方便调试，有时可以省去抓包的工作。

```python
# 仅需要修改的代码部分：

# 构建一个HTTPHandler 处理器对象，支持处理HTTP请求，同时开启Debug Log，debuglevel 值默认 0 
http_handler = urllib.request.HTTPHandler(debuglevel=1)

# 构建一个HTTPHSandler 处理器对象，支持处理HTTPS请求，同时开启Debug Log，debuglevel 值默认 0 
https_handler = urllib.request.HTTPSHandler(debuglevel=1)
```

### 2) **ProxyHandler,****代理**

使用代理IP，这是爬虫/反爬虫的第二大招，通常也是最好用的。

网站它会检测某一段时间某个IP 的访问次数，如果访问次数过多，它会禁止你的访问,所以这个时候需要通过设置代理来爬取数据,这个时候我们可以通过rulllib.request.ProxyHandler()设置代理

```python

import urllib.request

#构建代理Handler
proxy_handler = urllib.request.ProxyHandler({ 'http': 'http://127.0.0.1:9743'
})

opener = urllib.request.build_opener(proxy_handler)

#   只有使用opener.open()方法发送请求才使用自定义的代理，而urlopen()则不使用自定义代理。
response = opener.open('http://httpbin.org/get')

#  将opener应用到全局，之后所有的，不管是opener.open()还是urlopen()  发送请求，都将使用自定义代理。
# urllib.request.install_opener(opener)
# response = urlopen(request) print(response.read())
```

免费的开放代理获取基本没有成本，我们可以在一些代理网站上收集这些免费代理，测试后如果可以用，就把它收集起来用在爬虫上面。

如果代理IP足够多，就可以像随机获取User-Agent一样，随机选择一个代理去访问网站。

```python
import urllib.request import random

proxy_list = [
{"http" : "124.88.67.81:80"},
{"http" : "124.88.67.81:80"},
{"http" : "124.88.67.81:80"},
{"http" : "124.88.67.81:80"},
{"http" : "124.88.67.81:80"}
]

# 随机选择一个代理
proxy = random.choice(proxy_list)

# 使用选择的代理构建代理处理器对象
httpproxy_handler = urllib.request.ProxyHandler(proxy) 
opener = urllib.request.build_opener(httpproxy_handler)
request = urllib.request.Request("http://www.baidu.com/") 
response = opener.open(request)

print(response.read())
```

但是，这些免费开放代理一般会有很多人都在使用，而且代理有寿命短，速度慢，匿名度不高，

HTTP/HTTPS支持不稳定等缺点（免费没好货）。

匿名度：通常情况下，使用免费代理是可以看到真实IP的，所以也叫透明代理。透明代理的请求报头有X- Forwarded-For部分，值是原始客户端的   IP。()

所以，专业爬虫工程师或爬虫公司会使用高品质的私密代理，这些代理通常需要找专门的代理供应商购买，再通过用户名/密码授权使用（舍不得孩子套不到狼）。

### 3) **HTTPPasswordMgrWithDefaultRealm()**

HTTPPasswordMgrWithDefaultRealm()类将创建一个密码管理对象，用来保存 HTTP 请求相关的用户名和密码，主要应用两个场景：

1. 验证代理授权的用户名和密码    (ProxyBasicAuthHandler())

2. 验证Web客户端的的用户名和密码   (HTTPBasicAuthHandler())

**ProxyBasicAuthHandler(代理授权验证)**

如果我们使用之前的代码来使用私密代理，会报  HTTP  407  错误，表示代理没有通过身份验证：

urllib.request.HTTPError: HTTP Error 407: Proxy Authentication Required

所以我们需要改写代码，通过：

HTTPPasswordMgrWithDefaultRealm()：来保存私密代理的用户密码 ProxyBasicAuthHandler()：来处理代理的身份验证。

```python

import urllib.request

# 私密代理授权的账户
user = "user"

# 私密代理授权的密码
passwd = "password"

# 私密代理 IP
proxy = "61.158.163.130:16816"

# 1. 构建一个HTTP密码管理对象，用来保存需要处理的用户名和密码
#p = urllib.request.HTTPPasswordMgrWithDefaultRealm()

# 2. 添加账户信息，第一个参数realm是与远程服务器相关的域信息，一般没人管它都是写None，后面三个参数分别是代理服务器、用户名、密码
#p.add_password(None, proxy, user, passwd)

#  3.  构建一个代理基础用户名/密码验证的ProxyBasicAuthHandler处理器对象，参数是创建的密码管理对象
#	注意，这里不再使用普通ProxyHandler类了
#proxyauth_handler = urllib.request.ProxyBasicAuthHandler(p)


# BTW：
#   工作中常用下面的方式直接创建一个附带私密代理验证的处理器，使用更加简洁明了，并不需要上面3步的代码

# 1. 构建一个附带Auth验证的的ProxyHandler处理器类对象
proxyauth_handler = urllib.request.ProxyHandler({"http" : "user:password@61.158.163.130:16816"})

# 2. 通过 build_opener()方法使用这个代理Handler对象，创建自定义opener对象，参数包括构建的 proxy_handler
opener = urllib.request.build_opener(proxyauth_handler)

# 3. 构造Request 请求
request = urllib.request.Request("http://www.baidu.com/")

# 4. 使用自定义opener发送请求
response = opener.open(request)

# 5. 打印响应内容
print(response.read())
```



**HTTPBasicAuthHandler**处理器（**Web**客户端授权验证）

有些Web服务器（包括HTTP/FTP等）的有些页面并不想提供公共访问权限，或者某些页面不希望公开，但是可以让特定的客户端访问。那么用户在访问时会要求进行身份认证。

爬虫直接访问会报HTTP  401  错误，表示访问身份未经授权：

urllib.request.HTTPError: HTTP Error 401: Unauthorized

如果我们有客户端的用户名和密码，我们可以通过下面的方法去访问爬取：

```python
import urllib.request

# 用户名
user = "test"
# 密码
passwd = "123456"
# Web服务器 IP
url = "http://localhost:6666/"

# 1. 构建一个密码管理对象，用来保存需要处理的用户名和密码
p = urllib.request.HTTPPasswordMgrWithDefaultRealm()

#   2.  添加账户信息，第一个参数realm是与远程服务器相关的域信息，一般没人管它都是写None，后面三个参数分别是Web服务器、用户名、密码 p.add_password(None, url, user, passwd)

#  3.  构建一个HTTP基础用户名/密码验证的HTTPBasicAuthHandler处理器对象，参数是创建的密码管理对象
auth_handler = urllib.request.HTTPBasicAuthHandler(p)

# 4. 通过 build_opener()方法使用这些代理Handler对象，创建自定义opener对象，参数包括构建的 proxy_handler
opener = urllib.request.build_opener(auth_handler)

# 5. 可以选择通过install_opener()方法定义opener为全局opener urllib.request.install_opener(opener)

# 6. 构建 Request对象
request = urllib.request.Request("http://localhost:6666/")

# 7. 定义opener为全局opener后，可直接使用urlopen()发送请求
response = urllib.request.urlopen(request)

# 8. 打印响应内容
print(response.read())
```

### 4) **cookie**

HTTP是无状态的面向连接的协议，服务器和客户端的交互仅限于请求/响应过程，结束之后便断开，在下一次请求时，服务器会认为新的客户端。为了维护他们之间的链接，让服务器知道这是之前某个用户发送的请求，则必须在一个地方保存客户端的信息。

Cookie：通过在 客户端 记录的信息确定用户的身份。

 Session：通过在 服务器端 记录的信息确定用户的身份。

Cookie 是指某些网站服务器为了辨别用户身份和进行Session跟踪，而储存在用户浏览器上的文本文件，Cookie可以保持登录信息到用户下次与服务器的会话。

**Cookie属性**

Cookie是http请求报头中的一种属性

```
Cookie名字（Name）
Cookie的值（Value）
Cookie的过期时间（Expires/Max-Age）
Cookie作用路径（Path）
Cookie所在域名（Domain），
使用Cookie进行安全连接（Secure）。
```

前两个参数是Cookie应用的必要条件，另外，还包括Cookie大小（不同浏览器对Cookie个数及大小限制是有差异的）。

Cookie由变量名和值组成，根据 Netscape公司的规定，Cookie格式如下： Set－Cookie: NAME=VALUE； Expires=DATE；Path=PATH；Domain=DOMAIN_NAME；SECURE

**Cookie应用**

Cookies在爬虫方面最典型的应用是判定注册用户是否已经登录网站，cookie中保存中我们常见的登录信息，有时候爬取网站需要携带cookie信息访问或者在下一次进入此网站时保留用户信息简化登录或其他验证过程。

```python
import urllib.request

# 1. 构建一个已经登录过的用户的headers信息
headers = {
"Host":"www.baidu.com", "Connection":"keep-alive", "Cache-Control": "max-age=0" "Upgrade-Insecure-Requests": "1"
"User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.139 Safari/537.36"
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8"
"Accept-Encoding": "gzip, deflate, br" "Accept-Language": "zh-CN,zh;q=0.9"

#  重点：这个Cookie是一个保存了用户登录状态的Cookie
"Cookie": "BAIDUID=D1749A6566F1233FA8124AE8AD791F0D:FG=1; BIDUPSID=D1749A6566F1233FA8124AE8AD791F0D; PSTM=1523859359; MCITY=-257%3A; BD_UPN=12314753; BDUSS=9yQmhVbm5PalZCZmFnUFVoc35VY2VqSWs5NFQ4UEV1SEZjLXZLdWQ4RUMzUWhiQVFBQUFBJCQAAAAAAAAAAAEAAAC1 UD4D0P7M7NTC07AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAJQ4VoC UOFaem; ispeed_lsm=2; BD_CK_SAM=1; PSINO=7; BD_HOME=1; H_PS_PSSID=1435_21118_22160; sug=3;
sugstore=0; ORIGIN=0; bdime=21110"
}

# 2. 通过headers里的报头信息（主要是Cookie信息），构建Request对象
req = urllib.request.Request("http://www.baidu.com/", headers = headers)

# 3. 直接访问renren主页，服务器会根据headers报头信息（主要是Cookie信息），判断这是一个已经登录的用户，并返回相应的页面
response = urllib.request.urlopen(req)

# 4. 打印响应内容
print(response.read())
```

### 5) **HTTPCookiProcessor**

我们一般用的是http.cookijar，用于获取cookie以及存储cookie

```python
import http.cookiejar, urllib.request

# 声明一个cookijar对象
cookie = http.cookiejar.CookieJar()

#  利用HTTPCookieProcessor构建一个handler创建cookie处理器对象
handler = urllib.request.HTTPCookieProcessor(cookie)

# 构建Opener
opener = urllib.request.build_opener(handler)

#  以get方法访问页面，访问之后会自动保存cookie到cookiejar中
response = opener.open('http://www.baidu.com')

for item in cookie: 
    print(item.name+"="+item.value)
```

在上面的例子中我们将Cookie保存到cookiejar对象中，然后打印出了cookie中的值，也就是访问百度首页的

Cookie值.

同时cookie可以写入到文件中保存，有两种方式http.cookiejar.MozillaCookieJar和

http.cookiejar.LWPCookieJar()，当然你自己用哪种方式都可以

```python
import http.cookiejar, urllib.request

# 保存cookie的本地磁盘文件名
filename = "cookie.txt"

#   声明一个LWPCookieJar(有save实现)对象实例来保存cookie，之后写入文件
# cookie = http.cookiejar.LWPCookieJar(filename)

#   声明一个MozillaCookieJar(有save实现)对象实例来保存cookie，之后写入文件
cookie = http.cookiejar.MozillaCookieJar(filename)

#  使用HTTPCookieProcessor()来创建cookie处理器对象
handler = urllib.request.HTTPCookieProcessor(cookie) 
opener = urllib.request.build_opener(handler) 
response = opener.open('http://www.baidu.com')
cookie.save(ignore_discard=True,  ignore_expires=True)
```

同样的如果想要通过获取文件中的cookie做为请求的一部分去访问，可以通过load方式获取

```python
import http.cookiejar, urllib.request

# 创建一个LWPCookieJar(有load实现)对象实例
cookie = http.cookiejar.LWPCookieJar()

# 从文件中读取cookie内容到变量
cookie.load('cookie.txt', ignore_discard=True, ignore_expires=True)

#  使用HTTPCookieProcessor()来创建cookie处理器对象
handler = urllib.request.HTTPCookieProcessor(cookie)

# 通过  build_opener() 来构建opener
opener = urllib.request.build_opener(handler)
response = opener.open('http://www.baidu.com') print(response.read().decode('utf-8'))
```

