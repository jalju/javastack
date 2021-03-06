
Session是服务器用来保存用户操作的一系列会话信息，由Web容器进行管理。单机情况下，不存在Session共享的情况，分布式情况下，如果不进行Session共享会出现请求落到不同机器要重复登录的情况，一般来说解决Session共享有以下几种方案。

**1、session复制**

session复制是早期的企业级的使用比较多的一种服务器集群session管理机制。应用服务器开启web容器的session复制功能，在集群中的几台服务器之间同步session对象，使得每台服务器上都保存所有的session信息，这样任何一台宕机都不会导致session的数据丢失，服务器使用session时，直接从本地获取。

这种方式在应用集群达到数千台的时候，就会出现瓶颈，每台都需要备份session，出现内存不够用的情况。

**2、session绑定**

利用hash算法，比如nginx的ip_hash,使得同一个Ip的请求分发到同一台服务器上。

这种方式不符合对系统的高可用要求，因为一旦某台服务器宕机，那么该机器上的session也就不复存在了，用户请求切换到其他机器后么有session，无法完成业务处理。

**3、利用cookie记录session**

session记录在客户端，每次请求服务器的时候，将session放在请求中发送给服务器，服务器处理完请求后再将修改后的session响应给客户端。这里的客户端就是cookie。

利用cookie记录session的也有缺点，比如受cookie大小的限制，能记录的信息有限；每次请求响应都需要传递cookie，影响性能，如果用户关闭cookie，访问就不正常。但是由于

cookie的简单易用，可用性高，支持应用服务器的线性伸缩，而大部分要记录的session信息比较小，因此事实上，许多网站或多或少的在使用cookie记录session。

**4、session服务器**

session服务器可以解决上面的所有的问题，利用独立部署的session服务器（集群）统一管理session，服务器每次读写session时，都访问session服务器。

这种解决方案事实上是应用服务器的状态分离，分为无状态的应用服务器和有状态的session服务器，然后针对这两种服务器的不同特性分别设计架构。

对于有状态的session服务器，一种比较简单的方法是利用分布式缓存（memcached), 数据库等。在这些产品的基础上进行包装，使其符合session的存储和访问要求。

如果业务场景对session管理有比较高的要求，比如利用session服务基层单点登录（sso),用户服务器等功能，需要开发专门的session服务管理平台。

以上部分内容摘自《**大型网站技术架构：核心原理与案例分析**》一书，获取该书高清电子版，及更多架构技术书籍，可在公众号菜单中回复"**微信群**"，在对应的群文件中进行获取。
