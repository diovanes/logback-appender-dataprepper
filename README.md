# logback-http-appender

## Run Spring Boot application
```
mvn clean install
```

## pom.xml configure
```
...
    <dependency>
        <groupId>com.github.diovanes</groupId>
        <artifactId>logback-appender-dataprepper</artifactId>
        <version>1.0.1</version>
    </dependency>
...
```

## How to use

```xml
    <appender name="HTTP" class="com.github.diovanes.logback.appender.HttpJsonAppender">
        <method>post</method>
        <url>http://localhost:2021/log/ingest</url>
        <contentType>json</contentType>
        <headers>{"Content-Type": "application/json"}</headers>
        <reconnectDelay>10</reconnectDelay>
        <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <pattern>
                    <pattern>
                        {
                            "dataHora": "%date{\"yyyy-MM-dd'T'HH:mm:ss,SSS\", UTC}",
                            "produto": "teste1",
                            "ambiente": "DEV",
                            "nivel": "%level",
                            "logger": "%logger",
                            "mensagenErro": "%exception{short}",
                            "classeErro": "%class{0}",
                            "stackTrace": "%exception{full}",
                            "mensagem": "%message"
                        }
                    </pattern>
                </pattern>
            </providers>
        </encoder>
    </appender>

```

## Data Prepper 

### Docker
```
docker run --name data-prepper -p 2021:2021 -v pipelines.yaml:/usr/share/data-prepper/pipelines/pipelines.yaml opensearchproject/data-prepper:2.8.0
```

### pipeline.yaml
```
log-pipeline-http:
  workers: 8
  delay: 5
  source:
    http:
      health_check_service: true
      authentication:
        http_basic:
          username: "myuser"
          password: "mys3cret"
  buffer:
    bounded_blocking:
      buffer_size: 500
      batch_size: 100
  processor:
    - parse_json:
  route:
    - dev: '/ambiente == "DEV"'
    - hml: '/ambiente == "HML"'
  sink:
    - opensearch:
        hosts: ["https://os01:9200", "https://os02:9200", "https://os03:9200"]
        username: "admin"
        password: "admin"
        index: "logs_dev"
        routes: [dev]
    - opensearch:
        hosts: ["https://os01:9200", "https://os02:9200", "https://os03:9200"]
        username: "admin"
        password: "admin"
        index: "logs_hml"
        routes: [hml]
    - stdout:

```

## References

* [Chapter 4: Appenders](http://logback.qos.ch/manual/appenders.html)
* [logback-redis-appender](https://github.com/kmtong/logback-redis-appender)
