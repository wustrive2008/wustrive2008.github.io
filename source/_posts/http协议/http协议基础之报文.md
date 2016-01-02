title: http协议基础之报文
date: 2015-8-26 23:02:25
categories: http协议
tags: [http,报文,方法]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexoHTTP.png
---

http协议之报文
---

### 报文的语法

请求报文：
```
<method> <request-URL> <version>
<headers>

<entity-body>
```

响应报文：
```
<version> <status> <reason-phrase>
<headers>

<entity-body>
```

说明：
- 方法(method)
    客户端希望服务器对资源执行的动作，常见的有GET,POST等
- 请求URL
    请求资源的绝对路径或相对路径
- 版本
    格式：HTTP/<major>.<minor>
- 状态码
    表示服务器处理的结果，常见的有200,404,400,415,500,502等
- 原因短语
    对状态码的简单描述，如OK
- 首部(headers)
    可以有零个或多个首部，每个首部都包含一个名字，后面跟着一个冒号(:),然后是一个可选的空格，接着是一个值，最后是一个CRLF
- 实体的主体部分(entity-body)
    实体的主体部分包含一个由任意数据组成的数据块

### 首部

#### 通用首部
客户端和服务器端都可以使用的首部，如Date
Date: May, 4 Oct 2015 02:15:10 GMT

#### 请求首部
请求报文所特有的首部，为服务器提供一些额外的信息，如Accept首部用来告知服务器客户端会接受与其请求相符的任意媒体类型：
Accept: \*/\*

##### Accept首部
Accept首部为客户端提供了一种将其喜好和能力告知服务器的方式，包含它们想要什么，可以使用什么，以及最重要的，它们不想要什么。这样，服务器就可以根据这些额外信息，对要发送的内容做出更明智的决定。
Accept首部包括：
```
Accept                  告诉服务器能发送哪些媒体类型
Accept-Charset          告诉服务器能发送哪些字符集
Accept-Encoding         告诉服务器能发送哪些编码方式
Accept-Language         告诉服务器能发送哪些语言
TE                      告诉服务器可以使用哪些扩展传输编码

```

##### 条件请求首部
客户端有时希望为请求加上某些限制。比如，如果客户端已经有了一份文档副本，就希望只在服务器上的文档如客户端拥有的副本有所区别时，才请求服务器传输文档，这时就可以使用条件请求首部：
```
Expect                  允许客户端列出某请求所要求的服务器行为
If-Match                如果实体标记与文档当前的实体标记相匹配，就获取这份文档
If-Modified-Since       除非在某个指定日期之后资源被修改过，否则就限制这个请求
if-None-Match           如果提供的实体标记与当前文档的实体标记不相符，获取文档
If-Range                允许对文档的某个范围进行条件请求
If-Unmodified-since     除非在某个指定日期之后资源被未修改过,否则就限制这个请求
Range                   如果服务器支持范围请求,就请求资源的指定范围
```

##### 安全请求首部
服务器可以对客户端进行简单的身份认证
常见的安全请求首部有：
```
Authorization            包含了客户端提供给服务器，以便对其自身进行认证的数据
Cookie                   客户端向服务器传送一个令牌
```

#### 响应首部
服务器为客户端提供一些额外信息
如：
```
Age                         响应持续时间
Public                      服务器为其资源支持的请求方法列表
Retry-After                 如果资源不可用，在此日期或时间重试
Server                      服务器应用程序的软件名称和版本
```


#### 实体首部
实体首部指的是用于应对实体主体部分的首部。比如，可以用实体首部来说明实体主体部分的数据类型。
Content-Type：text/html; charset=utf-8

##### 内容首部
提供与实体内容有关的特定信息，说明了其类型，尺寸以及处理它所需的其他有用信息
如下：
```
Content-Base                    解析相对URL时使用的基础URL
Content-Encoding                对主体执行的任意编码方式
Content-Language                理解主体时最适宜使用的自然语言
Content-Length                  主体的长度或尺寸
Content-Location                资源实际所处的位置
Content-MD5                     主体的MD5校验和
Content-Range                   在整个资源中此实体表示的字节范围
Content-Type                    这个主体的对象类型

```

#### 扩展首部
非标准的自定义首部

### 方法

#### Get方法 Head方法
get方法用于请求服务器发送某个资源
Head方法与Get方法类似，但服务器在响应中只返回首部。不会返回实体的主体部分，这就允许客户端在未获取实际资源的情况下，对资源首部进行检查，可以：
- 在获取资源的情况下了解资源的情况
- 通过查看响应中的状态码，看看某个对象是否存在
- 通过查看首部，测试资源是否被修改

#### PUT方法
用于向服务器写入文档，在实际应用中很少用，多用POST实现PUT的功能

#### POST方法
向服务器输入数据，如提交表单数据

#### TRACE方法
发起一个"环回"诊断，行程最后一站的服务器会发送一个TRACE响应，并在响应主体中携带收到的原始报文

#### OPTIONS方法
请求服务器告知其支持的各种功能，可以询问服务器支持哪些方法。
服务器的响应如：
```
HTTP/1.1 200 OK
Allow: GET, POST, PUT
Content-Length: 0
```

### 状态码

#### 100~199信息性状态码

```
100     Continue        说明收到了请求的初始部分，请客户端继续。发送了这个状态码之后，服务器收到请求之后必须进行响应
101     Switching protocols     说明服务器正在根据客户端的指定，将协议切换成Update首部所列的协议

```

##### 客户端与100 Continue
如果客户端向服务器发送一个实体，并且愿意在发送实体之前等待100 Continue响应，那么客户端就要发送一个携带了值为100 Continue的Expect请求首部。如果客户端没有发送实体，就不应该发送100 Continue Expect首部，这样会使服务器误以为客户端要发送一个实体。

客户端发送了100 Continue的Expect首部后不应该永远在那等待服务器的响应。超过一定时间后，应该直接发送实体。

##### 服务器与100 Continue
如果服务器收到一个带有只为100 Continue的Expect首部请求，会用100 Continue响应或一条错误码来进行响应。服务器永远也不应该向没有发送100 Continue期望的客户端发送100 Continue 状态码

如果处于某种原因，服务器在有机会发送100 Continue之前就收到了部分或全部实体，说明客户端已经决定继续发送实体了，这时，服务器不需要发送这个状态码了。

##### 代理与100 Continue

如果代理从客户端收到了一条带有100 Continue期望的请求，如果下一跳服务器是HTTP/1.1兼容的，或者不知道下一跳服务器与哪个版本兼容，应该将Expect首部转发到下一跳服务器。如果知道下一跳服务器与HTTP/1.1之前的版本兼容，就应该以417 Expectation Failed错误进行响应。

#### 200~299 成功状态码
200 OK
201 Create
202 Accepted

#### 300~399 重定向状态码
304 Not Modified

#### 400~499 客户端错误状态码
400 Bad Request     请求错误
401 Unauthorized    需要认证或认证错误
403 Forbidden       请求被拒绝，有可能是资源权限问题
404 Not Found       未找到资源
405 Method Not Allowed  不支持的方法
408 Request Timeout     请求超时


#### 500~599 服务器错误状态码
500 Internal Sever Error    服务器内部错误
501 Not Implemented     超出服务器能力范围
502 Bad Gateway         网关错误，一般是代理服务器找不到下一条链路
503 Sevice Unavailable  暂时无法为请求提供你服务
504 Gateway Timeout     响应超时
505 HTTP Version Not Supported  不支持的协议版本

### 捐赠
如何觉得本文章对你有帮助，欢迎捐赠

![捐赠](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%94%AF%E4%BB%98%E5%AE%9D%E4%BB%98%E6%AC%BE%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%9C%80%E5%B0%8F.png)

