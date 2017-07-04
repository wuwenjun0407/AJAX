# AJAX

@(AJAX和DOS命令)

### AJAX是什么？
>Async(异步) Javascript And XML
>异步：客户端和服务器数据交互，不需要将整个页面刷新，只需要把操作的这部分更新一下，这就叫做局部刷新。（比如：表单提交的时候，值需要把表单里面的内容发送给后台，并不是需要刷新页面）

>AJAX一般都是用来请求页面的部分数据，再将获取出来的数据绑定带页面指定位置

### AJAX如何使用？
>四步：
-  1.创建一个XHR的异步对象(这种方式不兼容IE6及更低版本的浏览器，而要做兼容处理)
```
var xhr=new XMLHttpRequest()
```
-  2.打开一个URL地址并请求（发送请求之前的配置）
```
open([请求方式],[API接口]，[设置同步异步]，[user name],[user pass]);
[请求方式]：get系列：GET/DELETE/HEAD
          post系列：POST/PUT
[user name]：用户名 
[user pass]：用户密码
有些服务器为了安全考虑，值允许部分人可以访问，就只给部分人分配权限，访问的时候需要提供安全秘钥，一般的服务器就不需要这么麻烦，匿名访问就可以，所以只需要传前三个参数即可         
```
-  3.监听不同的状态，进行不同业务操作（onreadystatechange）
 **xhr.readyState:AJAX状态**
```
**xhr.readyState:AJAX状态**
0：unSend  未发送，刚开始创建了一个AJAX的实例（var xhr=new XMLHttpRequest()）
1：opend:已经打开 第二步open（"get",url,true）
2：headers_received 客户端已经收到服务器的响应头了
3：loading 服务器返回的响应主体正在传输
4：done  服务器返回的响应主体已经被客户端接受
xhr.status HTTP状态 通过这个状态码可以知道HTTP事物是否成功，以及失败的原因
[2开头]：代表成功
200：OK
[3开头]：也代表成功，只不过这个过程经过了特殊处理
301：重定向（报错：Moved Permanently）在新版的HTTP协议中代表永久重定向 比如访问http://www.360buy.com默认会跳转到JD页面
302：在新版的HTTP协议中代表临时转移（报错：Moved temporarily）==>服务器的负载均衡
304：获取的是缓存中的数据（网站性能优化的重要手段，我们一般将不怎么变静态资源JS/CSS/IMG等做成304缓存，以后直接在缓存中拿就可以了）
[4开头]：代表错误，一般都是客户端的错误
400：请求参数出错（报错：Bad Request）
401：无访问权限（报错：Unauthorized） 
403：请求接受到了，但是没有返回正常的结果，没有原因（报错：Forbidden）
404：请求的地址不存在（报错：Not Fond）
413：客户端传给服务器的内容超过了服务器愿意接受的范围
[5开头]：代表服务器错误
500：服务器的未知错误（报错：Internal Server Error）
503：服务器超负荷（报错：Server Unavailable）
```
**xhr.response：获取响应主体（一般不用）**
**xhr.responseText：获取响应主体内容，是text（字符串）格式的**
**xhr.responseXML：获取响应主体内容，是XLM格式的**
**xhr.getResponseHeader("Data")：获取响应头的指定内容**
**xhr.getALLResponseHeaders：获取所有的响应头**
**xhr.timeout：设置请求的超时时间，比如设置的是3s，超过3s就算请求失败，并且如果请求超时就会触发ontimeout事件**
**xhr.abort（）：终端当前AJAX请求，一旦中断请求，就会触发onabort事件**
**xhr.setRequestHeader([key],[value])：设置请求头信息** 

-  4.发送send（）AJAX这件事是从send开始的，之前都是在做准备工作，当xhr.readyState===4的时候结束


#### 面试问题
##### ET系列和POST系列区别？
>1.传参数的方式不同
-   get：通过URL+？+参数（xhr.open("get","/userInfo?name=xxx&age=100");send(null)）
-  post：请求主体(xhr.open("post","/userInfo");send(“name=xxx&age=100))

>2.get方式容易出现缓存，而post不会
-  原因：GET是通过URL上问号拼接参数的发昂视传给服务器数据的，如果当前传的参数跟上一次一样了，浏览器就会走他的记忆功能（缓存），以为你请求的是同一个URL，就会把之前的返回给你，这样有些需求是不可以的比如我想实时获取股票信息，就不能走缓存
-  清除缓存原理：保证URL每一次都不同，这就可以给每个URL后面拼接一个变量=随机数/时间戳
```
xhr.open("get","/userInfo?name=xxx&age=100&_="Math.randow());
xhr.open("get","/userInfo?name=xxx&age=100&_="(new Data.getTime());
```

>3.参数大小，URL传参数是有长度限制的，太长了就会被截取
-   get：是把数据放到URL上，但是浏览器的URL大小是有限制的，如果超过大小浏览器就会默认给你截断（每个浏览器不一样，一般谷歌是8kb,火狐7kb,IE约2kb）
-  post：数据在请求主体中，大小没有限制，但是在真实项目中，为了保证上传速度，一般限制在100kb以内
>4.安全性
-  get不安全：将数据放到URL上谁都可以看到
-  一般有重要的信息都会放到post请求


### AJAX的兼容处理问题
 
##### 创建异步对象
>正常情况下的浏览器
```
var xhr=new XMLHttpRequest()
```
>在IE6及以下兼容方法
```
 var xhr=new ActiveObject("Microsoft.XMLHTTP");
    var xhr=new ActiveObject("Msxml2.XMLHTTP");
    var xhr=new ActiveObject("Msxml3.XMLHTTP");
```
>解决办法：利用的是惰性思想
```
  function createXHR(){
        var xhr=null;
        var ary=[
                function(){
                    return new XMLHttpRequest()
                },
                function(){
                    return new ActiveObject("Microsoft.XMLHTTP");
                },
            function(){
                return new ActiveObject("Msxml2.XMLHTTP");
            },
            function(){
                return new ActiveObject("Msxml3.XMLHTTP");
            }
        ];
        for(var i=0;i<ary.length;i++){
            try{
                xhr=ary[i]();
                createXHR=ary[i];
                break;
            }catch(e){

            }
        }
        return xhr;
    }
    var xhr1=createXHR();
    var xhr2=createXHR();//这里执行的只是 createXHR=ary[i];赋值之后的函数，不会在循环
```
