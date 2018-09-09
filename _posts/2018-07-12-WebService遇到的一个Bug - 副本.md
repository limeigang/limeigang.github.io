---
layout:     post
title:      "WebService中遇到的一个Bug"
subtitle:   " \"一个逗号引发的“血案\""
date:       2018-07-12 23:00:00
author:     "Mr.Xu"
header-img: "img/post-bg-2018.png"
catalog: true
tags:
    - java
    - Bug
---

```
15:40:36,398  WARN JAXRSUtils:522 - No operation matching request path "/crm_management/services/customerService/customerLogin%3Ftelephone=13112345678,%20&password=123456" is found, Relative Path: /customerLogin%3Ftelephone=13112345678,%20&password=123456, HTTP Method: GET, ContentType: /, Accept: application/json,. Please enable FINE/TRACE log level for more details.

15:40:36,417  WARN WebApplicationExceptionMapper:73 - javax.ws.rs.ClientErrorException: HTTP 404 Not Found

```

​          今天遇到一个折腾了好久的Bug，在远程调用webservice的时候，一直报`No operation matching request path` 的错误!看bug显示应该是请求格式的问题，但是仔细检查，各种修改后都没有解决问题，

后来百度了解到有可能是cxf版本的问题，

![1531650941064](/img/Bug/1531650941064.png)

在更新了cxf的版本后，这个问题的确解决了。但是查找数据时，一直显示为null，但是我的数据库里明显有这两个数据。仔细查看发现电话号码后面不知为啥多了一个“，”号，所以才一直查询不到数据。

![1531651456797](/img/Bug/1531651456797.png)

又检查前端代码发现有两个`name`属性都为`telephone`，这样把两个数据都传到了后台，导致在通过模型注入的时候，实际的`telephone`属性为`13112345678`再加上一个空的字符串，所以会出现电话号码后面有一个逗号的问题。

![1531651502743](/img/Bug/1531651502743.png)



那么问题就可以解释了，在之前没有更新cxf版本的时候，系统无法识别”，“号，一直报`No operation matching request path` 这个问题，一直显示调用的路径有问题，其实当时路径上就有这个逗号，

![1531652013894](/img/Bug/1531652013894.png)

然而一直没有发现，在更新了版本后，估计框架可以识别了，把这里的逗号“，”和前面的电话号码一起当做了字符串到数据库进行查询，自然就一直查询不到对象了，多了一个逗号嘛！



折腾了一个下午，很郁闷，这样的小问题，以后应该避免，其实第一次报这个bug的时候，就有注意到这个逗号的问题，但是完全没有在意，以为调用服务传递多个参数的时候，就会有一个逗号进行分割，有这样"想当然"的想法问题所在还是对于源码的不清楚，就全凭自己瞎猜了。

以后敲代码找bug还是要认真，敬畏每一行代码。不过风险就是。。。。有可能秃头。。。。o(￣▽￣)ｄ