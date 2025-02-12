# ego-parent

该平台是一个综合性的B2C电商平台，商家入驻商城上架销售自家的产品，并且可以得到商城提供的各种服务，消费者可以通过平台选购自己心仪的商品。

项目基于dubbo分布式服务框架设计，整个项目包括后台管理系统、前台系统、搜索系统、单点登录系统、订单系统、购物车系统等。这样可以使每个功能模块独立出来，系统之间交互需要使用远程通信，虽然增加了接口开发工作量，但是降低了各系统之间的耦合度，增删一个功能不会影响其他功能模块。

因为项目是采用分布式的架构设计，各模块之间相互独立，访问路径不同，且Ajax不支持跨域请求数据，当首页页面异步请求后台管理系统加载商品类目时，会出现跨域受限的问题。解决这个问题使用的是jsonp，jsonp通过script标签的src可以跨域请求资源的特性，将请求的数据伪装成js脚本解析，再定义一个回调函数，获取传入的数据。当某个系统的业务需要请求其他系统的数据时，使用的是HttpClient通过Java代码模拟浏览器发送http请求。

为了节约资源、提高响应速度和减轻数据库的压力，使用了Mycat分库分表技术，主数据库用来增、删、改数据，从数据库用来查数据，并且使用redis缓存技术将首页大广告和商品类目以及未登录状态的购物车商品信息等不需要经常修改的数据存入到缓存中，Redis把数据以key—value的形式缓存到内存中。通过redis集群解决redis内存受限的问题，利用jedis对redis中的缓存数据进行管理。

由于分布式架构中，每个系统都是单独部署运行一个单独的tomcat，所以不能将用户的登录信息保存到session中，因此选择Redis+Cookie 来模拟 HttpSession 功能。当用户登录时，如果登录成功，就生成一个名为token的JSESSIONID作为key把用户信息保存到redis中，并把token保存到cookie中。

项目采用Nginx+Tomcat进行部署，nginx一方面做加载静态资源的服务器，另一方面来做反向代理和负载均衡。
