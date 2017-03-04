#Ajax
<p>
    <b>出现时间</b>：为2005年
    <b>技术发起人</b>：Jesse James Garrett
    <b>为何而出现</b>：为了解决向服务器请求额外的数据同时不卸载页面
    <b>全称</b>：Asynchronous JavaScript + XML
</p>
***
<p>
    <b>技术核心</b>XMLHttpRequest(后面简称为XHR)
    XHR为相服务器发送请求和解析服务器响应提供了流畅的接口
</p>
<p>
    虽然名字中包含XML，但是Ajax通信与数据格式无关，无需刷新页面即可从服务器取得数据，但不一定是XML数据
</p>
***
###XMLHttpRequest 对象
<p>
    
IE7+、Firefox、Opera、Chrome与Safari都支持原生的XHR对象在这些浏览器中创建中创建XHR     要像下面这样使用XMLHttpRequest构造函数。
<pre><code>
    var xhr = new XMLHttpRequest();
</code></pre>
而在早期的IE浏览器（IE7之前的版本）
<pre><code>
    function createXHR(){
        if(typeof arguments.callee.activeXString != 'string'){
            var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                            "MSXML2.XMLHttp"], i,len;
            for(i=0,len=versions.length;i < len;i++){
                try{
                    new ActiveXObject(versinos[i]);
                    arguments.callee.activeXString = versions[i];
                    break;
                }catch (ex){
                    //跳过
                }
            }
    }
        return new ActiveXObject(arguments.callee.activeXString);
    }
</code></pre>
</p>
***
####XHR 用法
<p>
    调用的第一个方法是open()
    这个方法接受三个参数
        1. 请求类型
        2. 请求的URL
        3. 是否异步的发送请求
    <pre><code>
        xhr.open('post','example.php','true');
        //1. 这段代码的意思是向example.php以post方法发送一个异步请求
        //2. URL是相对于执行代码的当前页面（当然可以使用绝对路径）
        //3. 调用open方法并不会发送请求，知识启动一个请求，准备发送
    </code></pre>
    如果想发送特定的请求，需要调用send（）方法
    <pre><code>
        xhr.open('post','example.php','true');
        xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
        var param = 'userName='+userName+'&password='+passWord; 
        xhr.send(param);
    </code></pre>
    这次是异步请求，在收到响应后，相应的数据会自动填充XHR对象的属性，
    相关的属性简介：
    1. responseText:作为响应主体北返回的文本。
    2. responseXML：如果响应内容类型是‘text/xml’或‘application／xml’,这个属性中将包含响应数据的XML DOM文档
    3. status:象印的HTTP状态
    4. statusText:HTTP状态的说明
    
    发送异步请求时，通过检测XHR对象的readyState属性，了解请求/响应 过程的当前活动阶段
    *0:未初始化。尚未调用open（）方法*
    *1:启动。已经调用open（）方法，但尚未受到响应*
    *2:发送。已经调用send（）方法，但尚未接收到响应*
    *3:接收。已经接收到部分响应数据*
    *4:完成。已经接收到全部响应数据，而且已经可以在客户端使用了*
    只要readyState属性的值由一个值变成另一个值，都会触发一次readystatechange时间
    在接受到响应之前还可以调用abort()方法来取消异步请求
        xhr.abort();
    调用这个方法之后，XHR对象会停止出发时间，也不再允许访问任何与响应有关的对象属性。
</p>
####GET请求
<p>
    使用get请求时经常发生一个错误就是查询字符串的个数有问题，产寻字符串中每个参数的名称和值都必须使用encodeURIComponent()进行编码，然后才能放到URL末尾
</p>
####POST请求
<p>
    他的使用频率仅次于GET，通常用于向服务器发送应该被保存的数据
    POST请求应该吧数据作为请求的主体提交，GET请求传统上不是这样。
    POST请求的主体可以包含非常多的数据，各式不限

    如果不设置Content-Type头部信息，那么发送给服务器的数据就不会出现在$_POST超全局变量中
    这时候要访问同样的数据，需要借助$HTTP_RAW_POST_DATA

    POST请求小号的资源会更多一些
</p>
###jquery上的Ajax
<p>
    <pre><code>
        $.ajax({
            type:'post',
            url:'./01.php',
            data:{code:code},
            dataType:'json',
            success:function(data){
                //这里写上数据请求成功后想要执行的东西
            }
        })
    </code></pre>
</p>
##跨域请求
<p>
    CORS（Cross-Origin Resource Sharing,跨源资源共享）
    背后思想：使用自定义的HTTP头部，让浏览器与服务器进行沟通，从而决定请求或者响应是否该成功
    *请求和响应都不包含cookie信息*
</p>
####其他跨与技术
<p>
    图像Ping
        最常用于跟踪用户点击页面活动或动态广告曝光次数。
        有两个主要的缺点：
                        1. 只能发送GET请求
                        2. 无法访问服务器的响应文本
    所以图像Ping只能用于浏览器与服务器间的单项通信

    这项技术是使用img标签，因为一个网页可以从任何网页中加载图像，不用担心跨域的问题。
    <pre><code>
        var img = new Image();
        img.onload = img.onerror = function(){
        alert('done!');
        };
        img.src = 'http://www.example.com/test?name=Nicholas'
    </code></pre>
</p>
<p>
    JSONP
    非常流行，看起来和JSON差不多，不过是被包含在函数调用中的JSON
    callback（{'name':'Nicholas'}）;

    JSONP由两部分组成：回调函数和数据。
    <b>回调函数</b>：当响应到来时应该在页面中调用的函数。
                    他的名字一般是在请求中指定的
    <b>数据</b>：传入回调函数中的JSON数据。
    <pre><code>
        典型的JSONP请求。
        http://freegeoip.ner/json/?callback=handleResponse
    </code></pre>
    
    实际上是动态的生成script标签，然后在标签中的src属性这里写上访问的url和数据，动态生成的时候是异步的，从服务器中返回函数的调用

    jquery中的JSONP
    <pre><code>
         ajax({
            url:"http://www.tom.com/jsonp.php",
            dataType:'jsonp',
            data:{username:'zhangsan',password:'123'},
            jsonp:'cb',//这里的名字指的是等号前面的键，后端根据这个键获取方法名，jquery的默认参数名称是callback）
            jsonpCallback:'abc',//这个属性的作用就是自定义回调函数的名字
            success:function(data){//这里的data是形参
                console.log(data.username,data.password);
            }
        });
    </code></pre>
</p>
<p>
    Web Sockets
    最让人静静了到的新浏览器API
    web sockets的目标是在一个单独的持久连接上提供全双工、双向通信。

    使用标准的HTTP服务器无法实现Web Sockets，只有支持这种协议的专门服务器才能正常工作。
    URL模式不再是http：／／而是ws：／／，加密的链接也不是https：／／而是wss：／／

    相对于HTTP协议的好处是：能够在客户端和服务器之间发送非常少量的数据，而不必担心HTTP那样直接寄的开销。
    传递的数据包很小，非常适合移动应用。

    使用自定义协议的缺点在于：制定协议的时间比定制JavaScript API的时间还要长
</p>
##小结
Ajax是无需刷新页面就能够从古物器取得数据的一种方法
核心对象XMLHttpRequest

同源策略是对XHR的一个主要约束，他为通信设置了“相同的域，相同的端口，相同的协议”
解决跨域请求的方案叫做CORS，最稳妥。
Comet是对Ajax的进一步扩展，让服务器几乎能够实时的向客户端推送数据。
实现Comet的手段有两个：长轮询和HTTP流
SSE是一种实现Comet交互的浏览器API