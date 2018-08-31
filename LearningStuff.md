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
