# Java Service Development Guide

Off the many options available for picking a framework to build Java Rest services in we've chosen [Dropwizard](http://www.dropwizard.io/1.0.6/docs/getting-started.html) (current version 1.0.6) + Maven.


### Configuration
Service configuration should be externalized from service code base in accordance with 12 Factor best practices. We can accomplish that by using [Dropwizard Template Config](https://github.com/tkrille/dropwizard-template-config) bundle. Add the maven dependency
```xml
<dependency>
    <groupId>de.thomaskrille</groupId>
    <artifactId>dropwizard-template-config</artifactId>
    <version>1.5.0</version>
</dependency>
```

... and add `TemplateConfigBundle` to the `Bootstrap` object..
```java
@Override
public void initialize(final Bootstrap<Configuration> bootstrap) {
    ...
    bootstrap.addBundle(new TemplateConfigBundle());
    ...
}
```


Sample Config file:
```yml
server:
  type: simple
  connector:
    type: http
    # replacing environment variables
    port: ${PORT}
logging:
  # with default values too
  level: ${LOG_LEVEL!'WARN'}
  appenders:
    # system properties also work
    - type: ${log_appender!'console'}

```

#### Logging

##### Request Id Tracing
Use [this](https://github.com/service-enabled/dropwizard-request-tracker) library allows us to intercept a request and generate a request UUID if none exists. Follow the steps outlined on the github page. Additionally add the following to config :
``` yaml
requestTracker:
  headerName: X-Request-Id
```

##### Logentires
Please refer to this library to setup Logentires with Dropwizard service - https://githttps://github.com/service-enabled/dropwizard-request-tracker
