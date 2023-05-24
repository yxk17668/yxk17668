---
title: nginx 解决session一致性
date: 2019-08-09 17:13:54
tags: 
    - nginx
    - session
    - spring-session
    - springboot
    - springMVC
toc: true 
---

##### 1. session 粘滞性

> 每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题

``` upstream backserver {
ip_hash;
server 192.168.0.14:88;
server 192.168.0.15:80;
}
```

> 但是有缺点，这存在单点风险，倘若我已经在192.168.0.14:88端口登录后，过段时间发现14服务器挂了（session时间未过期），那么这时候会访问到15服务器，那这时候需要重新登录，因为在拿14服务器上的JsessionId去15服务器请求发现不存在



##### 2. session 粘滞性

> 可以通过tomcat的server.xml文件进行配置，这样每个tomcat都会同步对应的session，那么此时即使某个tomcat被宕机了，也不影响服务
>
> 具体的tomcat可以参见如下： [tomcat官网对应集群配置](http://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html)

```xml
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="8">

          <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>

          <Channel className="org.apache.catalina.tribes.group.GroupChannel">
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/>
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="auto"
                      port="4000"
                      autoBind="100"
                      selectorTimeout="5000"
                      maxThreads="6"/>

            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
            </Sender>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatchInterceptor"/>
          </Channel>

          <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                 filter=""/>
          <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

          <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="false"/>

          <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
        </Cluster>

```



##### 3. session 共享

###### 	1. springboot版本

- 对应的项目中pom文件添加

  ```xml
  <dependency>  
          <groupId>org.springframework.boot</groupId>  
          <artifactId>spring-boot-starter-redis</artifactId>  
  </dependency>  
  <dependency>  
          <groupId>org.springframework.session</groupId>  
          <artifactId>spring-session-data-redis</artifactId>  
  </dependency>  
  ```

- 在对应的application.properties中配置redis信息

```
spring.redis.host=localhost  
spring.redis.port=6379 
```

- 添加开启spring session支持的注解@EnableRedisHttpSession

```java
@Configuration  
@EnableRedisHttpSession  
public class RedisSessionConfig {  
} 
```

- 添加验证的接口

```java
@Value("${server.port}")
    String port;

    @GetMapping("/session")
    public Object getSession(HttpServletRequest request){
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("SessionId", request.getSession().getId());
        map.put("ServerPort", "服务端口号为 "+port);
        return map;
    }
```

- 访问 [http://localhost:8080/session](http://localhost:8080/session)

- 访问 [http://localhost:8888/session](http://localhost:8888/session)

- 结果中的SessionId是一致的，却是由两个不同项目工程来提供服务。这样子，SpringSession 利用拦截器 Filter 帮我们在每个请求前进行了同步设置，达到了分布式系统中 session 共享

  ###### 2. springmvc 版本

- 首先在pom.xml文件中添加对应的jar

```xml
 <dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
    <version>1.2.1.RELEASE</version>
  </dependency>
  <dependency>
     <groupId>redis.clients</groupId>
     <artifactId>jedis</artifactId>
      <version>2.8.1</version>
  </dependency>
```

- 在对应springmvc-context.xml文件中配置

```xml
<bean id="redisHttpSessionConfiguration"
          class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration">
        <property name="maxInactiveIntervalInSeconds" value="600"/>
    </bean>

    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxTotal" value="100" />
        <property name="maxIdle" value="10" />
        <property name="testOnBorrow" value="true" />
    </bean>

    <bean id="connectionFactory"  class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" >
        <property name="poolConfig" ref="jedisPoolConfig" />
        <property name="port" value="6379" />
        <property name="hostName" value="192.168.1.11" />
        <property name="password" value="xxx" />
        <property name="timeout" value="30000" ></property>
    </bean >

    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate" >
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="keySerializer">
            <bean  class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
    </bean >

    <bean id="redisUtil" class="com.isea533.mybatis.service.RedisUtil" >
        <property name="redisTemplate" ref="redisTemplate" />
    </bean >
```

- 其次在web.xml中配置（非常重要，filter的名字必须是springSessionRepositoryFilter）

```xml
  <filter>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>springSessionRepositoryFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

> 访问项目路径结果相同