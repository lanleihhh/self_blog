# web服务器、web容器、web中间件

三者之间的关系类似于JDK、JRE、JVM之间的关系

可以认为Web服务器包含了Web中间件，而web中间件又包含了Web容器

## web服务器

web服务器是指提供web服务的计算机，也就是安装了web服务软件的计算机，用于给用户提供http服务，进行上网功能，比如apache，IIS、tomcat等提供了这样的功能，所以我们把他们叫做web服务器

> unix和Linux平台下使用最广泛的免费HTTP服务器是Apache服务器
>
> Windows平台的服务器通常使用IIS作为Web服务器

## web容器

1. Tomcat是[Apache](https://so.csdn.net/so/search?q=Apache)鼎力支持的Java Web应用服务器，由于它优秀的稳定性以及丰富的文档资料，广泛的使用人群，从而在开源领域受到最广泛的青睐。

2. Jboss作为Java EE应用服务器，它不但是Servlet容器，而且是EJB容器，从而受到企业级开发人员的欢迎，从而弥补了Tomcat只是一个Servlet容器的缺憾。


3. Resin也仅仅是一个Servlet容器，然而由于它优秀的运行速度，使得它在轻量级Java Web领域备受喜爱，特别是在互联网Web服务领域，众多知名公司都采用其作为他们的Java Web应用服务器，譬如163、ku6等。-在商用应用服务器里主要有：Weblogic、Websphere，其中Weblogic我也使用过很长一段时间，当时也只用其当Servlet容器，然而就在同等条件下，在性能及易用性等方面，要比Tomcat优秀很多。
4. 4.glassfish是Sun公司推出的Java EE服务器，一个比较活跃的开源社区,不断的通过社区的反馈来提高其的可用性,经过glassfish v1 glassfish v2 到今天的glassfish v3 ,它已经走向成熟.Glassfish是一个免费、开放源代码的应用服务，它实现了Java EE 5，Java EE 5 平台包括了以下最新技术：EJB 3.0、JSF 1.2、Servlet 2.5、JSP 2.1、JAX-WS 2.0、JAXB 2.0、 Java Persistence 1.0、Common Annonations 1.0、StAX 1.0等。支持集群,通过内存中会话状态复制,增强了部署体系结构的可用性与可伸缩性,它对集群有着很好的支持,可以简单到通过添加机器,就可轻松的提高网站的带负载能力,在解析能力方面,它对html的吞吐能力与apache服务器不分上下,就是tomcat所不能比的,支持目录部署,热部署,解决了tomcat对热部署能力的缺陷.在版本方面做的更加人性化,有开发时用的简化版,专门用于部署web项目的版本,还要完全符合j2ee标准的版本.-
