# 限流

## Provider端使用限流

用户在provider端使用限流策略，可以限制指定微服务向其发送请求的频率，达到限制每秒钟最大请求数量的效果。

### 注意事项

1. 限流策略的控制并不是绝对精确的，可能会有少量误差。
2. provider端的流量控制是业务层面的功能，不是安全意义上的流量控制，如需防止DDoS攻击，需要结合其他的一系列措施。
3. 流量控制是微服务级的，不是实例级的。例如一个consumer服务有三个实例，当对它们依赖的provider实例配置限流策略后，provider不会区分consumer的请求具体是由哪个实例发出的，而是汇总成微服务级的统计数据进行限流判断。

### 配置说明

限流策略配置在microservice.yaml文件中，相关配置项见表**QPS流控配置项说明**。要开启服务提供者端的限流策略，还需要在处理链中配置服务端限流handler，并添加pom依赖。

* microservice.yaml配置示例如下：
  ```yaml
  servicecomb:
    handler:
      chain:
        Provider:
          default: qps-flowcontrol-provider
  ```
* 添加handler-flowcontrol-qps的pom依赖：
  ```xml
  <dependency>
      <groupId>org.apache.servicecomb</groupId>
      <artifactId>handler-flowcontrol-qps</artifactId>
  </dependency>
  ```

**QPS流控配置项说明**

| 配置项 | 默认值 | 取值范围 | 是否必选 | 含义 | 注意 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| servicecomb.flowcontrol.Provider.qps.enabled | true | true/false | 否 | 是否启用Provider流控 | - |
| servicecomb.flowcontrol.Provider.qps.limit.\[ServiceName\].\[Schema\].\[operation\] | 2147483647（max int） | \(0,2147483647\]，整形 | 否 | 每秒钟允许的请求数 | 支持microservice/schema/operation三个级别的配置，后者的优先级高于前者 |
| servicecomb.flowcontrol.Provider.qps.global.limit | 2147483647（max int） | \(0,2147483647\]，整形 | 否 | provider接受请求流量的全局配置 | 没有具体的流控配置时，此配置生效 |

> **注意：**
> provider端限流策略配置中的`ServiceName`指的是调用该provider的consumer，而`schema`、`operation`指的是provider自身的。即provider端限流配置的含义是，限制指定consumer调用本provider的某个schema、operation的流量。

## Consumer端使用限流

用户在consumer端使用限流策略，可以限制发往指定微服务的请求的频率。

### 配置说明

限流策略配置在microservice.yaml文件中，相关配置项见下表。要开启服务消费者端的限流策略，还需要在处理链中配置消费端限流handler，配置示例如下：

```yaml
servicecomb:
  handler:
    chain:
      Consumer:
        default: qps-flowcontrol-consumer
```

QPS流控配置项说明

| 配置项 | 默认值 | 取值范围 | 是否必选 | 含义 | 注意 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| servicecomb.flowcontrol.Consumer.qps.enabled | true | Boolean | 否 | 是否启用Consumer流控 | - |
| servicecomb.flowcontrol.Consumer.qps.limit.\[ServiceName\].\[Schema\].\[operation\] | 2147483647  \(max int\) | \(0,2147483647\]，整形 | 否 | 每秒钟允许的请求数 | 支持microservice、schema、operation三个级别的配置 |




