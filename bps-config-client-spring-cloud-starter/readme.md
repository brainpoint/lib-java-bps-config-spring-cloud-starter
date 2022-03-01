
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.bpfaas/bps-config-client-spring-cloud-starter/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.bpfaas/bps-config-client-spring-cloud-starter/)
[![License](https://img.shields.io/github/license/bpfaas/java-bps-config-client-spring-cloud-starter)](https://opensource.org/licenses/MIT)


```html
<dependency>
    <groupId>com.bpfaas</groupId>
    <artifactId>bps-config-client-spring-cloud-starter</artifactId>
    <version>3.0.1.RELEASE</version>
</dependency>
```

SpringCloud配置中心客户端starter

## Config

bootstrap.properties

### Config server config:

```yaml
spring:
  application:
    name: config
  profiles:
    active: dev

---
spring:
  profiles: dev
  cloud:
    config:
      # 消息总线信息.
      bus.rabbitmq:
        username: username
        password: password
        host: localhost
        port: 5672
        virtual-host: /
      fail-fast: true   # 连接失败即中断.
      uri: ${BPFAAS_CONFIG_CENTER_URI}
      label: dev
      profile: dev
      retry:
        max-attempts: 6        # 配置重试次数，默认为6
        multiplier: 1.1        # 间隔乘数 默认1.1
        initial-interval: 1000 # 初始重试间隔时间，默认1000ms
        max-interval: 2000     # 最大间隔时间，默认2000ms
      token: ${BPFAAS_CONFIG_TOKEN_BASE_SERVICE} # 针对不用应用使用不同权限token.
```

## API

监听如下事件, 接收配置刷新通知.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.bus.event.RefreshRemoteApplicationEvent;
import org.springframework.cloud.context.refresh.ContextRefresher;
import org.springframework.context.ApplicationListener;

@Component
@Slf4j
public class RefreshRemoteListener implements ApplicationListener<RefreshRemoteApplicationEvent> {

    @Autowired
    private ContextRefresher contextRefresher;

    @Override
    public void onApplicationEvent(RefreshRemoteApplicationEvent event) {
        Set<String> keys = this.contextRefresher.refresh();
        if (keys.isEmpty()) {
            return;
        }
    }
}
```