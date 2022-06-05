## HTTP 缓存



### 目录

1. 分享的原因
2. HTTP 缓存技术
3. 缓存技术实践



### 分享的原因
    
- 一张图片“惹的祸”
- why


##### 一张图片“惹的祸”

<img  src="https://pic.cofu.ltd/picgo/202205292151169.JPG"  height="600">

Note: 在APP静态页面接入CDN后，每次前端同事升级完都会出现页面错乱的情况。


##### why

![](https://pic.cofu.ltd/picgo/202205292312839.png)

Note: 经过测试发现APP里面内嵌的浏览器总是会缓存旧版本html页面，然而旧版本的html中会加载文件名称带hash值的css样式文件，然后老版本的css已经在CDN和源站被删除，导致返回404状态码，最终导致页面样式加载不出来。



### HTTP 缓存技术

- 为什么需要缓存
- HTTP缓存流程
- HTTP缓存指令
- 缓存如何存取



##### 为什么需要缓存

- 提高页面加载速度
- 节省带宽
- 减少服务器器负载

![](https://pic.cofu.ltd/picgo/202205300004082.png)



##### HTTP缓存流程
![](https://pic.cofu.ltd/picgo/202206052359903.png)


###### 决策树视角
![](https://pic.cofu.ltd/picgo/202206040034728.png)


###### 时间视角
![](https://pic.cofu.ltd/picgo/202206040128646.png)


###### 缓存流程演示
<iframe src="//player.bilibili.com/player.html?aid=384655930&bvid=BV16Z4y147YR&cid=738899638&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" height="600px" width="1000px"> </iframe>



##### HTTP缓存指令


max-age：最常用指令，该指令是告诉缓存系统将HTTP请求缓存多久，单位为秒

```bash
Cache-Control: max-age=120
```


no-store: 该指令告诉缓存系统不缓存HTTP响应的数据
```bash
Cache-Control: no-store
```


no-cache: 该指令容易引起误解，不能从字面意思理解，它表示不能在不验证的情况下不能使用缓存，必须发验证请求到服务端，如果服务端返回304状态码，则可以使用缓存。
```bash
Cache-Control: no-cache
```


must-revalidate: 缓存过期前，缓存过期后，必须发验证请求到服务端，如果服务端返回304状态码，则可以使用缓存，一般与`max-age`指令搭配使用，如果使用`max-age`时没有加该指令，则默认是有的。
```bash
# 两者等价
Cache-Control: max-age=120, must-revalidate
Cache-Control: max-age=120
```


no-store、no-cache、must-revalidate比较
| 指令 	| 行为 	|
|---	|---	|
| no-store 	| 不使用缓存 	|
| no-cache 	| 使用缓存前必须验证 	|
| must-revalidate 	| 缓存有效期内可以使用，否则发起验证 	|


public,private

public: 表示HTTP响应可以被公共的缓存服务缓存
private: 表示HTTP响应只可以被浏览器缓存
![](https://pic.cofu.ltd/picgo/202206052359903.png)



##### 缓存如何存取


缓存的key是什么？


 HTTP内容协商
 | 请求头字段 	| 说明 	| 响应头字段 	|
|---	|---	|---	|
| Accept 	| 告知服务器发送何种媒体类型 	| Content-Type 	|
| Accept-Language 	| 告知服务器发送何种语言 	| Content-Language 	|
| Accept-Charset 	| 告知服务器发送何种字符集 	| Content-Type 	|
| Accept-Encoding 	| 告知服务器采用何种压缩方式 	| Content-Encoding 	|


请求头
```bash
Accept:*/*
Accept-Encoding:gzip,deflate,sdch
Accept-Language:zh-CN,en-US;q=0.8,en;q=0.6
```
响应头
```bash
Content-Type: text/javascript
Content-Encoding: gzip
```


Vary: 用户告诉缓存服务器如何生成key

key=URL+gzip
响应头
```bash
Content-Type: text/javascript
Content-Encoding: gzip
Vary: Accept-Encoding
```



### 缓存技术实践


踩坑、踩坑、接着踩坑。。。。。。


![](https://pic.cofu.ltd/picgo/202206060034782.png)


Web服务器（Jetty）配置

<jetty_dir>/etc/webdefault.xml
``` xml
<servlet>
    <servlet-name>default</servlet-name>
    <servlet-class>org.eclipse.jetty.servlet.DefaultServlet</servlet-class>
    ...
    <!-- 开启etag -->
    <init-param>
            <param-name>etags</param-name>
            <param-value>true</param-value>
    </init-param>
    <!-- 设置客户端缓存静态文件120s -->
    <init-param>
            <param-name>cacheControl</param-name>
            <param-value>private, max-age=120</param-value>
    </init-param>
    ...
</servlet>
```


CDN设置

静态文件缓存1天

踩坑演示

<iframe src="//player.bilibili.com/player.html?aid=384718573&bvid=BV1fZ4y147tq&cid=738999998&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" height="500px" width="800px"> </iframe>



参考：

<ul>
<li>https://www.freecodecamp.org/news/an-in-depth-introduction-to-http-caching-cache-control-vary/</li>
<li>https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching</li>
<li>https://imququ.com/post/vary-header-in-http.html</li>
</ul>


使用的测试URL及代码地址:
<ul>
<li>http://cofu.ltd:81/http_cache_test?type=normal&cc=private,max-age=120</li>
<li>http://cofu.ltd:81/http_cache_test?type=error0</li>
<li>http://cofu.ltd:81/http_cache_test?type=error1</li>
<li>演示代码地址：https://github.com/happinessandlove/learn_openresty</li>
<li>PPT代码地址：https://github.com/happinessandlove/http_cache_slid.git</li>
</ul>