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

## References

* [Chapter 4: Appenders](http://logback.qos.ch/manual/appenders.html)
* [logback-redis-appender](https://github.com/kmtong/logback-redis-appender)
