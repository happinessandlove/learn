## HTTP 缓存



### 目录

1. 分享的原因
2. HTTP 缓存技术
3. 实践



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
- 缓存类型


##### 为什么需要缓存

- 提高页面加载速度
- 节省带宽
- 减少服务器器负载

![](https://pic.cofu.ltd/picgo/202205300004082.png)







## External 3.1

Content 3.1


## External 3.2

Content 3.2


## External 3.3 (Image)

![External Image](https://s3.amazonaws.com/static.slid.es/logo/v2/slides-symbol-512x512.png)


## External 3.4 (Math)

`\[ J(\theta_0,\theta_1) = \sum_{i=0} \]`
