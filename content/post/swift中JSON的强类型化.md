---
title: "swift中JSON的强类型化"
date: 2016-01-20
draft: false
tags: [swfit,json,强类型]
categories: [swift]
---

如果你还不了解SwiftyJSON，请参看https://github.com/SwiftyJSON/SwiftyJSON。 

SwiftyJSON让你可以用subscript的形式访问JSON，然而这样依然不是我最终想要的效果，比如这种情况：   


<pre>request(.GET, &#34;https://httpbin.org/get&#34;)
            .responseJSON(completionHandler: { (ress) -&gt; Void in
                switch ress.result {
                case .Success(let myjson) :
                    
                    let myresult = JSON(myjson)
                  
                case .Failure :
                    print(&#34;error&#34;)
                }
               
            })</pre>

（我假定你也在用[https://github.com/Alamofire/Alamofire](https://github.com/Alamofire/Alamofire)，官方的示例中的json结果如下：




<pre>{
  &#34;args&#34;: {}, 
  &#34;headers&#34;: {
    &#34;Accept&#34;: &#34;text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8&#34;, 
    &#34;Accept-Encoding&#34;: &#34;gzip, deflate&#34;, 
    &#34;Accept-Language&#34;: &#34;zh-cn&#34;, 
    &#34;Host&#34;: &#34;httpbin.org&#34;, 
    &#34;User-Agent&#34;: &#34;Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_2) AppleWebKit/601.3.5 (KHTML, like Gecko) Version/9.0.2 Safari/601.3.5&#34;
  }, 
  &#34;origin&#34;: &#34;123.125.226.136&#34;, 
  &#34;url&#34;: &#34;http://httpbin.org/get&#34;
}</pre>



）

虽然你取到了myresult但是它依然是一个JSON类型的对象，要想获得具体的“headers”里的参数“Accept-Language”需要这样写： 

let theLang = myresult[&#34;headers&#34;][&#34;Accept-Language&#34;].stringValue 

虽然这样已经很简单，但是如果headers里的属性过多，每一个属性的名字你还能记得住嘛？所以强类型的结果就很重要了，我们最理想的状态应该是这样写： 

let theLang =myresult.headers.AcceptLanguage 

怎样才能实现这样的效果呢？

    把Json结果对应到实体上，然后把SwiftyJSON的返回值跟实体做对应，代码如下：




<pre>struct HttpHeader {
    var args:Args,headers:Headers,origin:String,url:String
    init(myJson:JSON){
        args = Args()
        headers = Headers(myJson: myJson[&#34;headers&#34;])
        origin = myJson[&#34;origin&#34;].stringValue
        url = myJson[&#34;url&#34;].stringValue
    }
}

struct Args {
}

struct Headers {
    var Accept:String,AcceptEndcoding:String,AcceptLanguage:String,Host:String,UserAgent:String
    
    init(myJson:JSON){
        Accept = myJson[&#34;Accept&#34;].stringValue
        AcceptEndcoding = myJson[&#34;Accept-Encoding&#34;].stringValue
        AcceptLanguage = myJson[&#34;Accept-Language&#34;].stringValue
        Host = myJson[&#34;Host&#34;].stringValue
        UserAgent = myJson[&#34;User-Agent&#34;].stringValue
    }
}</pre>



这样刚才的代码就可以用点的方式实现了。



          


<pre>        request(.GET, &#34;https://httpbin.org/get&#34;)
            .responseJSON(completionHandler: { (ress) -&gt; Void in
                switch ress.result {
                case .Success(let myjson) :
                    let r = HttpHeader(myJson: JSON(myjson))
                    //print(r)
                    r.headers.AcceptEndcoding
                case .Failure :
                    print(&#34;error&#34;)
                }
               
            })</pre>

如果你觉得上面的实体类写起来太累工作量太大，你可以考虑使用反射。这里我找到了一个[https://github.com/johnlui/JSONNeverDie](https://github.com/johnlui/JSONNeverDie)，但是我不推荐，主要原因是性能问题，如果实体类是一个其他类型的数组，就需要循环的去反射，性能必然下降，当然这也是我的猜测，毕竟我没有编码测试，同样这有篇文章供你参考[http://swift.gg/2015/11/23/swift-reflection-api-what-you-can-do/](http://swift.gg/2015/11/23/swift-reflection-api-what-you-can-do/)。



如果你对反射的性能带来的损失忽略不计的话，当然还有别的思路应该也可以实现，比如在序列化的时候利用反射把类型转换了，我收集了一些相关的文档供大家参考。

[https://github.com/evermeer/EVReflection](https://github.com/evermeer/EVReflection)：Swift的反射类库

[https://github.com/evermeer/AlamofireJsonToObjects](https://github.com/evermeer/AlamofireJsonToObjects)：利用Alamofire和EVReflection实现的强类型化，由于我一直没有找到解决如果实体类中包含其他类的引用时的办法所以放弃了。

[https://github.com/sketchytech/Aldwych_JSON_Swift](https://github.com/sketchytech/Aldwych_JSON_Swift)：关于这个类库这里有篇解释文章https://realm.io/news/swift-summit-anthony-levings-json-type-safety/ 






 
- - -
 