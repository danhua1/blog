---
title: 跨域
date: 2022-11-05 15:00:00
tags: 
  - js
  - 请求
categories: 
  - 日常开发
  - 跨域
---

# 一跨域

## 1 同源策略

### 浏览器同源策略限制请求

同源是指"协议+域名+端口"三者相同，即便两个不同的域名指向同一个ip地址，也非同源。

所以跨域形成的原因就是不同源造成的，域名不一样肯定是会跨域的。但是很少会有人会注意到端口也要需要一致。这个是会经常漏掉的一个点。

### 不同源会限制以下浏览器功能的使用

1. Cookie、LocalStorage 和 IndexDB 无法读取

2. DOM 和 Js对象无法获得

3. AJAX 请求不能发送

我们经常遇到的问题便是请求的发送，经常会在在控制台看到下面的报错

![](https://image-static.segmentfault.com/361/944/3619440048-5ac4cd7cec1a9_fix732)

看到那个Access-Control-Allow-Origin报错，看到这个报错的话，就是由于跨域造成的。

还有第一点，很多网站都会缓存用户登录信息到Cookie或者LocalStorage中，因为登录信息不会经常变化，为了防止重复请求，所以很多方案都会按照这样实现。但是我们可以想象一下，如果可以非同源获取Cookie或者LocalStorage，获取网站用户信息的手段又简单了。最简单的可以让用户在自己的网站隐式跳转到想要收集的网站，然后收集缓存信息即可。这样都不用去抓包去获取信息了，成本大大减少了。



### 有些标签是可以无视同源策略的，如以下这些

1. < img>
2. < script>
3. < link>

## 2 解决方案

### jsonp 通过请求中带一个回调函数解决

 因为script 是不受同源策略影响的，所以我们可以借用script来进行接口请求。然后请求通携带一个回调函数。我们通过回调函数来获取我们想要的值。但是这个方案有一点不好，就是只能发送get请求。

 代码实现

 ```
 <script>
  var script = document.createElement('script');
  script.type = 'text/javascript';   
  // 传参并指定回调执行函数为onBack
  script.src = 'http://www.aaa.com:8000/login?user=admin&callback=onBack';    
  document.head.appendChild(script);    
  // 回调执行函数
  function onBack(res) {
      // 获取后端接口返回的数据，对数据进行处理
    alert(JSON.stringify(res));
  } 
</script>

 ```
### document.domain + iframe跨域

仅限主域相同，子域不同的跨域应用场景

实现原理：两个页面都通过js强制设置document.domain为基础主域，就实现了同域

具体实现就不展示了，用的很少

### location.hash + iframe

实现原理： a欲与b跨域相互通信，通过中间页c来实现。 三个页面，不同域之间利用iframe的location.hash传值，相同域之间直接js访问来通信。
具体实现：
A域：a.html -> B域：b.html -> A域：c.html，
a与b不同域只能通过hash值单向通信，b与c也不同域也只能单向通信，但c与a同域，所以c可通过parent.parent访问a页面所有对象。

### window.name + iframe跨域

window.name属性的独特之处：name值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）。
通过iframe的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到本地域。这个就巧妙地绕过了浏览器的跨域访问限制，但同时它又是安全操作。

### postMessage跨域

postMessage一般用于解决以下问题
 1. 页面和其打开的新窗口的数据传递
 2. 多窗口之间消息传递
 3. 页面与嵌套的iframe消息传递
 4. 上面三个场景的跨域数据传递

 postMessage 主要还是用在iframe中比较多。当打开的页面不在同源的时候。同时两个页面需要互相通信的时候，就可以使用postMessage来进行通信，实际场景可以参考微前端场景，主应用和子应用之间的通信。

代码实现
```
// localhost:8020

(function(){
		  // 接收127.0.0.1的数据
		  window.addEventListener
		  ('message', function(e) {
		    alert('data from 127.0.0.1 ---> ' + e.data);        
		    var data = JSON.parse(e.data);        
		    if (data) {
		      data.number = 16;            
		      // 127.0.0.1
		      window.parent.postMessage(JSON.stringify(data),
		                                'http://127.0.0.1:8020/');
		    }
		  }, false);
		})()

// 127.0.0.1:8020

    <body>
		<iframe id="iframe" 
        src="http://localhost:8020/%E5%AE%9E%E7%94%A8%E5%B0%8F%E5%B7%A5%E5%85%B7/%E6%B1%82%E8%A7%A3%E4%BF%A9%E6%95%B0%E7%BB%84%E4%BA%A4%E9%9B%86/new_file.html?__hbt=1655432144450" 
        style="display:none;">
		</iframe>
	</body>
	<script type="text/javascript">
		var iframe = document.getElementById('iframe');
		iframe.onload = function() {
			var data = {
				name: 'aym'
			};
			// 向domain2传送跨域数据
			iframe.contentWindow.postMessage(JSON.stringify(data),
				'http://localhost:8020');
		};
		// 接受domain2返回数据
		window.addEventListener('message', function(e) {
				alert('data from domain2 ---> ' + e.data);
			},
		false);
	</script>

```

补充一个小知识，localhost和127.0.0.1 不是同源的，虽然解析是后都是127.0.0.1。最上面也解释过了，哪怕两个域名解析后是同一个ip也是非同源的。




