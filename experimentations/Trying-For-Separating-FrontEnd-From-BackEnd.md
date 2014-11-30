前后端分离的实现在SPA（Single-Page App）中应用较为广泛，如AngularJS，React 使用Ajax技术与后端通过RESTFul方式进行数据的请求与发送，

如我们的数据统计就是通过ng（AngularJS）的SPA项目，但它并不适用于我们V直播的项目（如SEO体验较差）。

考虑到之前曾听到的未来可能会换到JAVA的后端服务的可能性，重新看了一遍淘宝UED的”淘宝前后端分离的思考与实践“的文章，下面两张图是我对他们结构的理解。

----

### 结构

1.经典的结构

![image](http://ww1.sinaimg.cn/large/63f7a9b1gw1emt8dfzf8aj20qu0bywf0.jpg)

2.前后端分离的结构

![image](http://ww3.sinaimg.cn/large/63f7a9b1gw1emt8eii2lbj20xt0cr751.jpg)

----

### 优势

1.后端实现业务逻辑和数据的查询计算，独立部署后端服务。（例如后端服务部署在8080端口）

2.前端路由，MVC，实现相对于后端的独立，自主控制视图层。（同样实现独立部署如部署在80端口）

    ## 这里的Model如上图所示，是与8080端口的后端进行交互

3.通过Server（Nginx模块）判断客户端请求类型，自助引导至不同的模块，仅需要一次判断，后续无需重复判断。

4.前端通过步骤3的预处理，可以在页面渲染前对例如css/images/js资源进行压缩处理，如置换渲染所需资源到所需的CDN（如icon/icon-mobile/icon@1x/icon@2x）

5.传统的SPA通常由于资源的处理时间问题（网络环境影响），在完成前可能出现”空白页“（等待阶段），这里可以参考BigPipe方式，持续输出。

    ## 虽然可以通过一些手段实现渲染完成前的一些预处理，但采用这里说到的方法，实现上更加优雅。

6.相对于传统，这样的分离更加明确各自的职责，后端职责更加明确，前端自由度更高，耦合度却有所降低。

----

### 疑问

1.关于通信效率

用node做前端的一部分，MODEL（这里应该是伪Model了，毕竟他只是通过http之类的方式）与后端（如java的逻辑、运算服务器）的通讯效率问题。

效率问题，放在整个项目的生命周期里的话，是缩短的，所以这部分，我认为效率增加了。

2.关于前后端定义

有人对于这里存在不同的理解，其实之前就说过了，这里是对也许进行了分离，将MVC中的VC部分纳入前段，M部分交过后端，也许你不认可，但是这种的分离，给了前端更大的自由度，也让后端能够更专注于业务本身，而非交互逻辑。

我认为，所谓前端，是指一切与用户/客户端的交互相关的部分。

3.关于职责明确

前后端分离的源动力在于系统的分离，使得团队人员的职责明确，过去后端参与太多交互相关的内容，对他们来说，其实是在浪费他们的时间和精力，例如tp中（$this->display('index')）这样的东西，每次你一改动就让他们改，不觉得eggTeng？（别说什么模板之类的，同样的功能，我们有N种实现方法，我们只是在找一种更好的，更合理的对吧~）

4.关于使用与否

生命在于折腾，不是为了技术而技术，技术是在改善。

这样的讨论的目的是根据具体应用场景来定的，比如我需要一个庞大的数据运算模块，这里我也许可以用C来实现，如果为了语言统一，大可以将C写成node的组件，可当我们面对的时十台百台服务器，但并非每一台都要做运算，有必要每一台都要一个这样的node组件么？

成员职责的划分，项目模块的划分，是源动力吧~


----

### 试验

话说就是参考上面的图来做，所以结构请参考前面的内容，不过顺便说一下吧...

#### 简介

Nginx作为服务器入口，对所有请求进行转发，即设置一个反向代理，将80端口的请求全部转发到我们的nodejs应用，这里我设置的是8084端口。

补充一下，在CentOS上设置配置一下iptables，禁止外部访问端口，因为内部不仅仅只有这样应用，可能还会有其他的服务，例如我还有一个跑在内网的tp服务器在做接口，然后这个请求，并不是直接用nodejs进行http请求去获取数据，而是通过nginx进行映射，当然，你可以有更多的实现，说到底，只是为了未来进行负载均衡。

#### Demo

暂时还没到完整的试验阶段，团队又有一个博客，作为一个曾经忠实的WP粉到现在的WP半黑（其实只是懒，不想登录上去发文章），在Github上面建立了一个Repo用来放平时写的Markdown文档，使用Github Api来获取数据，并Render到这个Demo的页面上。

***这里Github暂时充当了我们的Model，MVC中我们尝试分离的，就是Model这一块。***

##### URL：
[http://to-fuck.me/](http://to-fuck.me/)

##### Markdown Files:
https://github.com/thonatos/Mt.Notes.And.Documents

##### Resources
* NodeJS
* Marked.js
* Gulp.js
* SpmJS
* SeaJS

##### Other

* Github Api拿到的md文件是base64的，需要转换一下：

	```
	var _raw = new Buffer(result.content, result.encoding).toString('utf8');
	var _html = marked(_raw);
	```

* 由于需要从Github上拿数据，程序跑在国外的一台VPS上，拿数据速度快一点。

* 作为前端狗，未来自然在其中加入很多蛋疼的功能，console大法你好。




——————————（我是牛逼的分割线。）

未完待续。