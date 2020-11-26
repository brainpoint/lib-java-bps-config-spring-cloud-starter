
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.bpfaas/bps-config-server-spring-cloud-starter/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.bpfaas/bps-config-server-spring-cloud-starter/)
[![License](https://img.shields.io/github/license/bpfaas/java-bps-config-server-spring-cloud-starter)](https://opensource.org/licenses/MIT)


```html
<dependency>
    <groupId>com.bpfaas</groupId>
    <artifactId>bps-config-server-spring-cloud-starter</artifactId>
    <version>0.0.1</version>
</dependency>
```

SpringCloud配置中心服务端starter

## Config

bootstrap.properties

### Config server config:

```yaml
spring:
  application:
    name: config
  profiles:
    active: vault,git, dev    # 注意加上vault,git

---
spring:
  profiles: dev
  cloud:
    # 使用vault存储私密信息.
    vault:
      host: ${BPFAAS_VAULT_HOST}
      port: ${BPFAAS_VAULT_port}
      scheme: ${BPFAAS_VAULT_SCHEME}
      authentication: TOKEN
      token: ${BPFAAS_VAULT_TOKEN}
      connection-timeout: ${BPFAAS_VAULT_CONNECT_TIMEOUT}
      read-timeout: ${BPFAAS_VAULT_READ_TIMEOUT}
      config:
        order: -10
      ssl:
        trust-store: classpath:dev/vault-keystore.jks
        trust-store-password: changeit
    # config使用git+vault做应用配置.
    config:
      # 消息总线配置.
      bus.rabbitmq:
        username: username
        password: password
        host: localhost
        port: 5672
        virtual-host: /
      server:
        encrypt:
          enabled: true
        vault:
          backend: secret
          host: ${BPFAAS_VAULT_HOST}
          port: ${BPFAAS_VAULT_port}
          scheme: ${BPFAAS_VAULT_SCHEME}
          kv-version: 1
          timeout: ${BPFAAS_VAULT_CONNECT_TIMEOUT}
          skip-ssl-validation: true  # 勿在生产环境使用.
        git:
          default-label: master
          search-paths: config
          skip-ssl-validation: false
          force-pull: true
          deleteUntrackedBranches: true
          uri: ${BPFAAS_GIT_URI}
          username: username
          password: password
```

## API

发送如下请求通知所有客户端配置刷新. `/actuator/bus-refresh`

```bash
curl -X POST http://localhost:8888/actuator/bus-refresh
```

发送如下请求通知指定服务的具体节点配置刷新. `/actuator/bus-refresh/{busId}`

```bash
curl -X POST http://localhost:8888/actuator/bus-refresh/serviceName:8888:c01b9ae0eb94423caddf435edc941265
```