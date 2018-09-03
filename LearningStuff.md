一、jetty.xml 配置文件理解
   
1、jetty启动 使用org.mortbay.xml.XmlConfiguration作为启动类，然后读取jetty.xml
2、jetty.xml配置文件相当于定义xml命令脚本，载入时自动根据说明创建相应对象。

<Configure id="Server" class="org.mortbay.jetty.Server">  --配置文件的root元素，读取到会创建一个空的Server对象;

<Set name="ThreadPool">  
  
  <New class="org.mortbay.thread.QueuedThreadPool">  
    <Set name="minThreads">10</Set>  
    <Set name="maxThreads">200</Set>  
    <Set name="lowThreads">20</Set>  
    <Set name="SpawnOrShrinkAt">2</Set>  
  </New>  
  
  <!-- Optional Java 5 bounded threadpool with job queue   
  <New class="org.mortbay.thread.concurrent.ThreadPool">  
    <Set name="corePoolSize">50</Set>  
    <Set name="maximumPoolSize">50</Set>  
  </New>  
  -->  
</Set>       -- 创建线程池对象，设置基本属性，调用setThreadPool方法将其设置为当前server的线程池。

<Call name="addConnector">  
  <Arg>  
      <New class="org.mortbay.jetty.nio.SelectChannelConnector">  
        <Set name="host"><SystemProperty name="jetty.host" /></Set>  
        <Set name="port"><SystemProperty name="jetty.port" default="8080"/></Set>  
        <Set name="maxIdleTime">30000</Set>  
        <Set name="Acceptors">2</Set>  
        <Set name="statsOn">false</Set>  
        <Set name="confidentialPort">8443</Set>  
 <Set name="lowResourcesConnections">5000</Set>  
 <Set name="lowResourcesMaxIdleTime">5000</Set>  
      </New>  
  </Arg>  
</Call>  

<Call name="addConnector">  
  <Arg>  
      <New class="org.mortbay.jetty.bio.SocketConnector">  
        <Set name="port">8081</Set>  
        <Set name="maxIdleTime">50000</Set>  
        <Set name="lowResourceMaxIdleTime">1500</Set>  
      </New>  
  </Arg>  
</Call>  --调用方法为server添加connector

<Set name="handler">  
  <New id="Handlers" class="org.mortbay.jetty.handler.HandlerCollection">  
    <Set name="handlers">  
     <Array type="org.mortbay.jetty.Handler">  
       <Item>  
         <New id="Contexts" class="org.mortbay.jetty.handler.ContextHandlerCollection"/>  
       </Item>  
       <Item>  
         <New id="DefaultHandler" class="org.mortbay.jetty.handler.DefaultHandler"/>  
       </Item>  
       <Item>  
         <New id="RequestLog" class="org.mortbay.jetty.handler.RequestLogHandler"/>  
       </Item>  
     </Array>  
    </Set>  
  </New>  
</Set>   --调用server的setHandler方法为当前的server设置handler属性，创建的handlercollection，包括三个实际的handler

2018-09-03 dubbo配置文件的理解(使用到的)

<dubbo:application>应用配置信息
name 当前应用名称

<dubbo:registry >注册中心配置，多个注册中心可以声明多个registry标签
id:注册中心引用beanId,
address:注册中心服务器地址，如果地址没有端口缺省为9090，同一集群内的多个地址用逗号分隔，不同集群的注册中心，请配置多个<dubbo:registry>标签
protocol注册中心地址协议，支持dubbo,http,local三种
port 注册中心缺省端口，当address没有带端口时使用此端口作为缺省值
timeout:注册中心请求超时时间
group 设置注册根节点

<dubbo:provider> 服务提供者缺省配置
<dubbo:service>服务提供者暴露服务配置 服务提供者暴露服务配置

interface：必填，服务接口名
ref:必填，服务对象实现引用
version：服务版本，建议使用两位数版本，不是必填标签
group：服务分组，当一个接口有多个实现，则可以用分组区分

<dubbo:consumer> 服务消费者缺省配置
<dubbo:reference> 服务消费者引用服务配置
id 服务引用beanid 必填
    interface 服务接口名，必填
    version:服务版本，与服务提供者版本一致
    group：服务分组
    timeout:服务方法调用超时时间
    retries:远程服务调用重试次数，不包括第一次调用
服务提供者声明服务接口，服务实现，注册地址
消费者声明引用的beanid 引用的服务接口，注册地址。

web.xml配置
<context-param>
<param-name>contextConfigLocation</param-name>
<param-value>locationValue</param-value>
</context-param>
用来声明整个web应用范围内的上下初始化参数
name-参数名称 必须唯一
value-参数值 

初始化过程：
1.启动web项目时，容器会读web.xml配置文件中的两个节点<listener>和<context-param>
2.然后容器创建一个ServletContext上下文对象，应用范围内整个web项目都使用这个上下文
3.接着容器读取<context-param>转化为键值对交给ServletContext.
4.容器创建<listener>中的类实例，即创建监听(listener定义的类可以是自定义的,但须继承ServletContextListener).
5.在监听的类中会有一个contextInitialized(ServletContextEvent event)初始化方法，在这个方法中可以通过event.getServletContext().getInitParameter("contextConfigLocation") 来得到context-param 设定的值。在这个类中还必须有一个contextDestroyed(ServletContextEvent event) 销毁方法.用于关闭应用前释放资源，比如说数据库连接的关闭.
6.得到context-param的值后，就可以做操作。注意,这个时候你的WEB项目还没有完全启动完成.这个动作会比所有的Servlet都要早。
   
   由上面的初始化过程可知容器对于web.xml的加载过程是context-param >> listener  >> fileter  >> servlet

