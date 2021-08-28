## HTTP 头部信息
HTTP请求头提供了关于请求，响应或者其他的发送实体的信息。HTTP的头信息包括通用头、请求头、响应头和实体头四个部分。头标格式：`<name>: <value><CRLF>`

- 通用头标：即可用于请求，也可用于响应，是作为一个整体而不是特定资源与事务相关联
- 请求头标：允许客户端传递关于自身的信息和希望的响应形式
- 响应头标：服务器用于传递自身信息
- 实体头标：定义被传送资源的信息。即可用于请求，也可用于响应

### 一、HTTP通用头
|  头信息 | 说明 |
| :-: | :-: |
| [Cache-Control](浏览器缓存.md#1强缓存) | Cache-Control 指定请求和响应遵循的(强)缓存机制 |
| Pragma | `Pragma: no-cache` 等同于 `Cache-Control: no-cache` ，兼容 HTTP/1.0 |
| [Connection](#connection) | 表示是否需要持久连接。（HTTP 1.1默认进行持久连接） |
| Date | 表示消息发送的时间，服务器响应中要包含这个头部，因为 `强缓存` 在评估资源过期时间时要用到 |
| [Transfer-Encoding](#transfer-encoding) | 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码 |
| Upgrade | 向服务器指定某种传输协议以便服务器进行转换（如果支持） |
| Via | 列出从客户端到 OCS 或者相反方向的响应经过了哪些代理服务器，他们用什么协议（和版本）发送的请求。 |
### 二、HTTP请求头
|  头信息 | 说明 |
| :-: | :-: |
| [Accept](#accept-系列字段解析) | 指定客户端能够接收的内容类型 |
| [Accept-Charset](#accept-系列字段解析) | 浏览器可以接受的字符编码集 |
| [Accept-Encoding](#accept-系列字段解析) | 浏览器申明自己接收的内容压缩编码类型 |
| [Accept-Language](#accept-系列字段解析) | 浏览器申明自己接收的语言 |
| [Authorization](#authorization-认证) | 当客户端接收到来自服务器的 `WWW-Authenticate` 响应时，用该头部来回应自己的身份验证信息给服务器 |
| If-Match | 表示这是一个条件请求。在请求方法为 `GET` 和 `HEAD` 的情况下，服务器仅在请求的资源满足此首部列出的 `ETag` 值时才会返回资源。而对于 `PUT` 或其他非安全方法来说，只有在满足条件的情况下才可以将资源上传。 |
| [If-None-Match](浏览器缓存.md#2协商缓存) | 配合 `ETag` 实现协商缓存 |
| [If-Modified-Since](浏览器缓存.md#2协商缓存) | 配合 `Last-Modified` 实现协商缓存 |
| If-Unmodified-Since | 使得当前请求成为条件式请求：只有当资源在指定的时间之后没有进行过修改的情况下，服务器才会返回请求的资源 |
| If-Range | 使 `Range` 头字段在一定条件下起作用：当字段值中的条件得到满足时，`Range` 头字段才会起作用，同时服务器回复 `206` 状态码，以及 `Range` 头字段请求的相应部分；如果字段值中的条件没有得到满足，服务器将会返回 `200` 状态码，并返回完整的请求资源。字段值中既可以用 `Last-Modified` 时间值用作验证，也可以用 `ETag` 标记作为验证，但不能将两者同时使用。 `If-Range` 头字段通常用于断点续传的下载过程中，用来自从上次中断后，确保下载的资源没有发生改变。 |
| Range | 告知服务器返回文件的哪一部分。在一个  `Range` 首部中，可以一次性请求多个部分，服务器会以 `multipart` 文件的形式将其返回。如果服务器返回的是范围响应，需要使用 `206` `Partial Content` 状态码。假如所请求的范围不合法，那么服务器会返回  `416` `Range Not Satisfiable` 状态码，表示客户端错误。服务器允许忽略 `Range` 首部，从而返回整个文件，状态码用 `200` |
| Proxy-Authorization | 是一个请求首部，其中包含了用户代理提供给代理服务器的用于身份验证的凭证。这个首部通常是在服务器返回了 `407` `Proxy Authentication` 响应状态码及 `Proxy-Authenticate` 首部后发送的 |
| Host | 指明了请求将要发送到的服务器主机名和端口号。如果没有包含端口号，会自动使用被请求服务的默认端口（比如 `HTTPS` URL使用443端口，`HTTP` URL使用80端口）。所有 `HTTP/1.1` 请求报文中必须包含一个 `Host` 头字段。对于缺少 `Host` 头或者含有超过一个 `Host` 头的`HTTP/1.1` 请求，可能会收到 `400` `（Bad Request）`状态码。 |
| Referer | 　浏览器向服务器表明自己是从哪个网页URL发起当前请求 |
| User-Agent | 包含了一个特征字符串，用来让网络协议的对端来识别发起请求的用户代理软件的应用类型、操作系统、软件开发商以及版本号 |
| [Origin](跨域.md) | CORS 中，请求来自哪个源 |
| [Access-Control-Request-Method](跨域.md) | 用来列出浏览器的CORS请求会用到哪些 HTTP请求方法 |
| [Access-Control-Request-Headers](跨域.md) | 一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段 |

### 三、HTTP响应头
|  头信息 | 说明 |
| :-: | :-: |
| Age | 包含消息对象在缓存代理中存贮的时长，以秒为单位 |
| Server | 服务器表明自己是什么软件及版本等信息 |
| Accept-Ranges | 服务器表明自己是否接受获取其某个实体的一部分（比如文件的一部分）的请求。`bytes`：表示接受，`none`：表示不接受 |
| Proxy-Authenticate | 指定了获取 proxy server （代理服务器）上的资源访问权限而采用的身份验证方式。代理服务器对请求进行验证，以便它进一步传递请求。`Proxy-Authenticate` 首部需要与 `407` `Proxy Authentication` 响应一起发送。 |
| [Access-Control-Allow-Origin](跨域.md) | CORS 响应时 `Origin` 字段的值，或者是一个 `*`，表示接受任意域名的请求 |
| [Access-Control-Allow-Credentials](跨域.md) | 表示是否允许发送Cookie |
| [Access-Control-Expose-Headers](跨域.md) | CORS请求时，`XMLHttpRequest` 对象的 `getResponseHeader()` 方法能拿到的额外字段 |
| [Access-Control-Allow-Methods](跨域.md) | 它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法 |
| [Access-Control-Allow-Headers](跨域.md) | 一个逗号分隔的字符串，表明服务器支持的所有头信息字段 |
| [Access-Control-Max-Age](跨域.md) | 用来指定本次预检请求的有效期，单位为秒 |
### 四、HTTP实体头
|  头信息 | 说明 |
| :-: | :-: |
| Allow | 服务器支持哪些请求方法（如GET、POST等） |
| Location | 表示客户应当到哪里去提取文档，用于将接收端定位到资源的位置（URL）上。同时设置状态码为 `302` |
| [Content-Encoding](#accept-系列字段解析) | 文档的编码方法 |
| [Content-Language](#accept-系列字段解析) | 服务器告诉浏览器理解主体时最适宜使用的自然语言 |
| Content-Length | 服务器告诉浏览器响应对象的长度 |
| [Content-Type](#accept-系列字段解析) | 响应文档属于什么MIME类型 |
| [Etag](浏览器缓存.md#2协商缓存) | 响应资源的特征值，与 `If-None-Match` 实现协商缓存 |
| [Expires](浏览器缓存.md#1强缓存) | 资源过期时间，用于 强缓存 |
| [Last-Modified](浏览器缓存.md#2协商缓存) | 文档的最后改动时间，与 `If-Modified-Since` 一起实现协商缓存 |

### 值域详解
#### Connection
- Close：告诉服务器，在完成本次请求的响应后，断开连接，不要等待本次连接的后续请求了
- Keepalive：告诉服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求
-  Keep-Alive：如果浏览器请求保持连接，则该头部表明希望服务器保持连接多长时间（秒），如Keep-Alive：300。

#### Transfer-Encoding
对于非持久连接，浏览器可以通过连接是否关闭来界定请求或响应实体的边界，而对于 `长连接`，这种方法显然不奏效。尽管已经发送完所有数据，但浏览器并不知道这一点，它无法得知这个打开的连接上是否还会有新数据进来，只能傻傻地等待。   

**解决方案：**
1. Content-Length   
浏览器可以通过 Content-Length 的长度信息，判断出响应实体已结束。  
但实际应用中，有些时候实体长度并没那么好获得
2. Transfer-Encoding: chunked  
在头部加入 `Transfer-Encoding: chunked` 之后，就代表这个报文采用了 `分块编码`。这时，报文中的实体需要改为用一系列分块来传输。每个分块包含`十六进制的长度值` 和 `数据`，。最后一个分块长度值必须为 `0`，对应的分块数据没有内容，表示实体结束

#### Accept 系列字段解析
对于 `Accept` 系列字段的介绍分为四个部分: `数据格式`、`压缩方式`、`支持语言` 和 `字符集`。
1. 数据格式  
`HTTP` 从 `MIME type` 取了一部分来标记报文 body 部分的数据类型，这些类型体现在 `Content-Type` 这个字段，当然这是针对于发送端而言，接收端想要收到特定类型的数据，也可以用 `Accept` 字段。
具体而言，这两个字段的取值可以分为下面几类:
   - text： text/html, text/plain, text/css ...
   - image: image/gif, image/jpeg, image/png ...
   - audio/video: audio/mpeg, video/mp4 ...
   - application: application/json, application/javascript, application/pdf ...
1. 压缩方式  
当然一般这些数据都是会进行编码压缩的，采取什么样的压缩方式就体现在了发送方的 `Content-Encoding` 字段上， 同样的，接收什么样的压缩方式体现在了接受方的 `Accept-Encoding` ]段上。这个字段的取值有下面几种：
   - gzip: 当今最流行的压缩格式
   - deflate: 另外一种著名的压缩格式
   - br: 一种专门为 HTTP 发明的压缩算法
1. 支持语言  
对于发送方而言，还有一个 `Content-Language` 字段，在需要实现国际化的方案当中，可以用来指定支持的语言，在接受方对应的字段为 `Accept-Language`
1. 字符集
最后是一个比较特殊的字段, 在接收端对应为 `Accept-Charset`，指定可以接受的字符集，而在发送端并没有对应的字段, 而是直接放在了 `Content-Type` 中

以一张图来总结:  
![accept](../../images/accept.png)

#### Authorization 认证
`WWW-Authenticate` 是一种简单的用户身份认证技术。  
优点: 方便  
缺点：在传输过程中采用的加密方式为 `BASE-64`，密码几乎是透明的  

1. 服务器收到请求后，首先会解析发送来的数据中是否包含有：`Authorization: Basic XXXX=` 请求头
1. 如果没有，那么服务器会发送HTTP信息头 `WWW-Authenticate: Basic realm=""` 到浏览器，要求浏览器发送合法的用户名和密码到服务端，同时返回 `401` 的状态码
1. 用户输入`用户名：admin, 密码：123456` 后，浏览器将以下面这种格式将数据发送给服务器端：`Authorization: Basic YWRtaW46YWRtaW4=`。
`Authorization: Basic` 为 `WWW-Authenticate` 认证的标准HTTP信息头  
`YWRtaW46YWRtaW4=` 是经 `BASE-64` 加密后的用户名和密码