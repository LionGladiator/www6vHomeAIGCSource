﻿---
title: Nginx优化
date: 2020-03-26 12:07:27
tags:
  - Nginx
categories:
  - 分布式
  - 中间件
  - nginx
---

<p hidden></p>
<!-- more -->

## 一. Nginx 参数优化

###  反向代理
<code>
proxy_cache: 10M(重要)
proxy_cache_path /data/nginx_cache/ levels=1:2 keys_zone=my_zone:10m inactive=300s max_size=5g;
</code>



### tls ssl
<code>
ssl_session_cache builtin:1000 shared:SSL:10m;  /// 一天内连接上的用户， 不需要再协商秘钥
ssl_session_cache builtin:1000 shared:SSL:10m;  /// 1m -> 4000个https连接
ssl_session_timeout 10m; /// 10分钟
ssl_protocols TLSv1.2;  /// 版本号
</code>

### 非安全请求重定向：
<code>
No redirect:  无重定向 
Redirect： 301-302 -》 转到https站点 
</code>

### gzip
<code>
gzip on;
gzip_comp_level 5;
gzip_http_version 1.1;   /// 注意：gzip 在1.1上有效， http2.0上是无效的
gzip_min_length 256;
gzip_types application/atom+xml ... 
gzip_proxied any;
gzip_vary on;
</code>


###  worker 
<code>
worker_connections  16384;  ///    一个worker有16384/2=8,192‬个链接 .
                                   两个事件， 一个读事件， 一个写事件。 
                                   越多的connections对应更多的内存消耗。
Default: worker_connections 512;                                 
</code>

<code>
高级选项：
worker connections的内存池（pools）， 更少的的内存碎片。一般是nginx自动分配的， 不用分配。
Default: 	connection_pool_size 256|512
Default: 	request_pool_size 4k;
</code>

### lua 分配的内存（暂时没有使用）
<code>
lua_shared_dict configuration_data 5M;
lua_shared_dict certificate_data 16M;
应用场景: 集群流控,  多个worker之间的内存的共享。
</code>


### http的keeplive（一个tcp的链接，上面有多个http的请求）  非tcp keeplive 
<code>
keepalive_disable;  /// 没有设置
keepalive_timeout  75s; // 默认值
keepalive_requests 100; // 默认值  一个tcp请求中可发100个http请求
</code>

### 二. 测试用例
URL：logsearch.sh.pre.urtc.com.cn

### case1
input：
1000用户并发, 短连接， 非keepalive的   

result：
链接数  40000+  
tps 4000+   
avg 百ms   

### case2
input：
1500用户并发，短连接， 非keepalive的    

result：
链接数 30000+，  
tps 4000+ ，   
avg 200ms+  

