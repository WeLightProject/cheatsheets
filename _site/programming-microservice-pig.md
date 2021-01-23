# 微服务简介 #

pig基于Spring Boot 2.4,Spring Cloud 2020 & Alibaba,OAuth2的微服务RBAC 权限管理系统.

## spring Boot 与 Spring Cloud 关系 ##

Spring Boot 是 Spring 的一套快速配置脚手架，可以基于spring Boot 快速开发单个微服务，Spring Cloud是一个基于Spring Boot实现的云应用开发工具；Spring Boot专注于快速、方便集成的单个个体，Spring Cloud是关注全局的服务治理框架；spring Boot使用了默认大于配置的理念，很多集成方案已经帮你选择好了，能不配置就不配置，Spring Cloud很大的一部分是基于Spring Boot来实现。

## spring Cloud Alibaba ##

Spring Cloud Alibaba是阿里的微服务解决方案，阿里巴巴结合自身微服务实践,开源的微服务全家桶，在Spring Cloud项目中孵化成为Spring Cloud的子项目。

[已被收录入spring Cloud官网]("https://spring.io/projects/spring-cloud-alibaba")

第一代的Spring Cloud标准中很多组件已经闭源停更,如：Eureak,zuul等。所以Spring Cloud Alibaba很有可能成为Spring Cloud第二代的标准实现，所以许多组件在业界逐渐开始使用，已有很多成功案例。值得一提的是Spring Cloud Alibaba对Dubbo做了很好的兼容，同时也提供了一些强大的功能，如 Sentinel 流控 ，Seata 分布式事务，Nacos 服务发现与注册等等。

[Discovery spring Cloud Alibaba 官方推荐开源项目]("https://github.com/Nepxion/Discovery")

# 功能模块介绍 #

pig项目并未全套整合Alibaba相关组件,仅仅是集成了核心功能

## Nacos ##


### Nacos简介 ###

Nacos是Alibaba开源组件,致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。
[官网](https://nacos.io/zh-cn/docs/what-is-nacos.html)
[Github](https://github.com/alibaba/nacos)


### 项目中对应 ###

在项目中对应 **pig-register**模块,Nacos默认的端口为8848端口,通过http://IP:8848/Nacos 进行访问.

在项目中所有木块启动配置均通过xxxx.yml进行配置.
yml是映射文件,映射到相关模块的配置对象,在Idea中可以通过提示按钮,查看对象中存在的配置属性,及相关的注释.  
从配置中可以看出当前nacos所有的线上配置均存储与pig_config数据库中
<a href="https://imgchr.com/i/sW4Y8I"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW4Y8I.png" alt="sW4Y8I.png"></a>

### 服务注册Nacos ###

已pig-auth授权服务为例,如下图:
<a href="https://imgchr.com/i/sW4wqS"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW4wqS.png" alt="sW4Y8I.png"></a>
最上面是运行时的端口,下面是运行的服务ID,cloud.nacos 就是注册到Nacos中的配置.
config 中配置在Nacos中的注册内容.
server-addr: 中配置发现nacos的地址,这边统一配置pig-register:8848,通过Host映射对应到部署的Nacos.
file-extension: 配置文件的结尾
shared-configs: 配置文件的名称 

```
	- application-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
	// application对应上面spring.application.name->映射为pom.xml中的artifactId,即pig-auth
	// ${spring.profiles.active} 即映射下面的@profiles.active@ 最终映射为根目录下的pom.xml中的 dev
	// 最后 ${spring.cloud.nacos.config.file-extension} 及上面的文件结尾 yml
	// 所以最后该服务在nacos的配置文件未 pig-auth-dev.yml
```
group: 服务的分组配置,默认为DEFAULT_GROUP 在分组集群管理时使用

还有更多的相关设置,例如心跳包的相关设置等.

### Nacos的负债均衡 ###

Nacos与gateway已经实现了默认的负载均衡,请求时会自动平均分配.
若需要实现多的负债功能,可以整合**Ribbon**. 在当前模项目中,已经在pig-register模块中添加了Ribbon模块.

<a href="https://imgchr.com/i/sW41Ve"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW41Ve.png" alt="sW4Y8I.png"></a>
通过@Bean注入的形式,可以使用不同的负载均衡模式,Ribbon已经默认实现了七种负载均衡策略.
[ribbon负载简单介绍](https://www.cnblogs.com/xuweiweiwoaini/p/13764522.html)

图中使用的**NacosRule**是Nacos基于Ribbon实现的一套负载策略,基于Nacos中服务实例权重在进行负载的.
`有效性待测试`


### Nacos简单详解 ###

Nacos是分布式集群化的核心,所有服务均在此注册,并且发送心跳包保持对应链接.同时其他的服务同时访问nacos获取其他服务的地址,确定请求的详细路径
同时Nacos管理了所有的服务相关配置,让服务仅需配置最简单的基本配置(Nacos的注册地址),其他的所有配置均在Nacos中注册,并提供了对应的配置文件管理,修改等功能.便于运维


## Gateway ##

顾名思义就是网关,用于处理所有请求的中转站,配合Nacos解决集群化部署时服务器的集群管理,负载均衡等相关功能.

### 项目中配置 ###

**pig-gateway** 项目中的网关模块,代码并不多,处理了一下验证码,传递数据加解密相关内容.
配置文件如下.

<a href="https://imgchr.com/i/sW485d"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW485d.png" alt="sW4Y8I.png"></a>
除了Nacos其他的服务配置基本大同小异,都是在本地设置端口号,Nacos注册地址,对应的配置文件,然后所有的配置交由Nacos去管理.

<a href="https://imgchr.com/i/sW43UH"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW43UH.png" alt="sW4Y8I.png"></a>
核心配置如下,在配置中声明对应的服务,访问路径

```
spring:
  cloud:
    gateway:
      routes:
        # 认证中心
        # 服务ID,对应其他服务启动的时候spring.application.name
        - id: pig-auth
          # 对应请求转发的地址,也可以是外部链接
          uri: lb://pig-auth
          predicates:
			# 匹配路径,当时请求auth/**的接口,就进行转发.
            - Path=/auth/**
          filters:
            # 验证码处理
            - ValidateCodeGatewayFilter
            # 前端密码解密
            - PasswordDecoderFilter
        #UPMS 模块
        - id: pig-upms-biz
          uri: lb://pig-upms-biz
          predicates:
            - Path=/admin/**
          filters:
            # 限流配置
            - name: RequestRateLimiter
              args:
                key-resolver: '#{@remoteAddrKeyResolver}'
                redis-rate-limiter.replenishRate: 100
                redis-rate-limiter.burstCapacity: 200
```

在routes中配置对应的内容


## sentinel流量保护 ##

sentinel 是面向分布式服务架构的流量控制组件，主要以流量为切入点，从流量控制、熔断降级、系统自适应保护等多个维度来帮助您保障微服务的稳定性。

当调用链路中某个资源出现不稳定，例如，表现为 timeout，异常比例升高的时候，则对这个资源的调用进行限制，并让请求快速失败，避免影响到其它的资源，最终产生雪崩的效果。

sentinel就是应付高并发或者其他的情况下接口异常,或者未响应时进行的降级熔断处理.

### 项目中的sentinel ###

项目中**sentinel**位于**pig-visual->pig-sentinel-dashborad**主要是可视化的管理界面功能.

对应的配置如下

<a href="https://imgchr.com/i/sW4aKf"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW4aKf.png" alt="sW4Y8I.png"></a>
这里可以看到有两个配置文件. **application.yml** 和 **bootstrap.yml**
**bootstrap.yml**为服务启动配置.主要就是注册到nacos.
**application.yml**作为启动配置文件,设定了前端页面访问的相关端口账号密码等.


### 降级熔断 ###

在启动了**pig-sentinel-dashborad**服务之后可以通过5003端口查看sentinel的控制台,在控制台中可以看到正在运行服务实例,然后对对应的接口来进行**限流****降级**处理.

<a href="https://imgchr.com/i/sW4t2t"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW4t2t.png" alt="sW4Y8I.png"></a>
在降级之后,对应的服务内部就会有一个降级处理的地方,默认的在**common**里.

<a href="https://imgchr.com/i/sW4NxP"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src=“https://s3.ax1x.com/2021/01/20/sW4NxP.png" alt="sW4Y8I.png"></a>
一个是限流的处理,一个是降级熔断的默认处理方式. 总的来说,返回结果就是
```
	return R.failed(ex.getLocalizedMessage());
```

在跨服务的过程中,一样可能出现问题,同时跨服务调用也是一个问题.项目中采用了 openfeign 的跨服务调用框架.
简单总结就是在对应的服务下按照标准创建api模块,然后创建Service,接着创建factory去生成失败的时候 fallback的实现类.  在fallback的实现类中去处理失败操作.

<a href="https://imgchr.com/i/sW4JPA"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src=“https://s3.ax1x.com/2021/01/20/sW4JPA.png" alt="sW4JPA.png"></a>
在高并发的情况下,不重要的子系统崩掉可以通过失败处理将要执行的操作写入失败记录的数据库,然后通过定时任务服务将失败的操作正常的去执行.

### 跨服务事务处理 ###

在跨服务的时候可以通过[seate](http://seata.io/zh-cn/)来进行事务的管理.在添加了seata之后,在需要进行事务管理的业务方法上添加**@GlobalTransactional**注解即可.

** 目前项目中并未集成seate **

还可以使用pigx中使用的事务框架基于 LCN 4.1 深度定制，事务操作和代码耦合度极低，支持注册中心的事务发现和自动管理，用法完全兼容LCN 。
[pigx文档介绍,及演示视频](https://pig4cloud.com/doc/pigx/pigx-tx)


## spring Boot Amdin ##

Spring Boot Admin 是用来管理 Spring Boot 应用程序的一个简单的界面。主要用来查看日志文件,在线调整日志文件级别,服务系统环境,内存等相关信息.

对应项目中的**pig-visual-pig-monitor**

启动服务之后通过配置文件中的5001端口即可访问Spring Boot界面.


## codegen ##

主要用于生成模板代码的...介绍太少,没太整明白使用方式.
与业务逻辑无关.

对应项目中的**pig-visual-pig-codegen**

## xxl-job ##


XXL-JOB是一个分布式任务调度平台，其核心设计目标是开发迅速、学习简单、轻量级、易扩展。现已开放源代码并接入多家公司线上产品线，开箱即用。

[官网地址](https://www.xuxueli.com/xxl-job/#1.1%20%E6%A6%82%E8%BF%B0)

目前没有使用场景,看到的介绍是在配合服务降级时使用.
`例如电商平台购物流程,部分服务熔断降级了,在降级处理中将日志写入数据库,然后通过XXL-JOB定时启动任务来去添加上对应的操作`



# 项目模块介绍 #


## pig-common ##

核心包作为基础依赖库,配置在**pig-common**中,作为基础让其他模块进行引用.

### core ###

核心配置包,配置了一下授权,校验,存储数据库参数校验等相关的东西

### datasource ###

管理数据库连接核心包,当需要使用多数据库源时,需要进行对应的配置

### feign ###

跨服务调用核心OpenFeign模块的集成,例如upms-api模块,就是引用该模块实现跨服务调用功能,然后在security中调用用户登陆.

### job ###

任务调度模块的对所有项目的注入入口

### log ###

项目中的log基础模块,spring Boot Ademin的日志查看的数据来源.

### mybatis ###

项目中的mybatis-plus注入的地方,需要使用增强的数据库操作时,再此编写对应的操作类,然后通过AutoConfiguration中进行注入

### security ###

身份权限校验,使用的oauth2来进行校验.
当前项目的身份校验逻辑均在此编写.

### swagger ###

在线的API文件+接口调试.
当服务部署完成后可以通过<http://pig-gateway:9999/swagger-ui/index.html>

<a href="https://imgchr.com/i/sW4dr8"><img style="display: inline-block; width: 100%; max-width: 100%; height: auto;" src="https://s3.ax1x.com/2021/01/20/sW4dr8.png" alt="sW4dr8.png"></a>
上面输入账号密码.下面输入 test test然后点击授权,即可完成授权,在网页中请求接口.


## pig-auth ##

授权服务,所有服务的授权令牌均在此处理.

## pig-curriculum ##

课程模块,所有课程的功能再此实现

## pig-exam ##

考试模块,考试相关的内容

## pig-exper ##

实验模块,所有实验相关的内容

## pig-gateway ##

网关服务,用于处理所有的请求转发

## pig-progress ##

进度服务,学习进度等. 目前空白

## pig-register ##

Nacos服务,用于注册所有的服务,管理配置.

## pig-upms ##

用户服务,用于管理所有用户增删改查的服务

## pig-visual ##

外部服务,包含spring Boot Admin,xxl-Job,sentinel,codegen.
