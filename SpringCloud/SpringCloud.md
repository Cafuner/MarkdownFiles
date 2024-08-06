## mac安装、使用nacos

http://www.taodudu.cc/news/show-5758226.html?action=onClick





## SpringCloud配置管理如何进行热更新？

有两种方式：

1. 在使用配置的类上加@RefreshScope注解。
2. 使用@ComponentProperties注解，将配置信息封装在一个POJO中。





## 微服务会从nacos中读取的配置文件：

1. [服务名]-[spring.profile.active].yaml，环境配置
2. [服务名].yaml，默认配置，多环境共享

优先级：[服务名]-[环境].yaml >[服务名].yaml > 本地配置





## Feign自定义配置方式：

有两种配置方式：

### 1.在SpringBoot配置文件中配置

```yaml
feign:
	client:
		config:
			default: #这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
				loggerLevel: FULL # 日志级别
```

### 2.使用配置类配置

首先在配置类中声明需要自定义的bean。

```java
public class FeignClientConfiguration {
	@Bean
    public Logger.Level feignLogLevel(){
		return Logger.Level.BASIC;
    }
}
```

然后需要把配置类放到下面两个注解中的一个：

1. @EnableFeignClients(defaultConfiguration = FeignClientConfiguration.class) 。这是全局配置。
2. @FeignClient(value = "userservice", configuration = FeignClientConfiguration.class)。这是只对某个client配置。 











