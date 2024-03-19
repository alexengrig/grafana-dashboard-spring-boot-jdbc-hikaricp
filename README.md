# Grafana Dashboard: _Spring Boot JDBC & HikariCP_

> To use this dashboard, import it from
> [the official website](https://grafana.com/grafana/dashboards/20729-spring-boot-jdbc-hikaricp)
> or use
> [dashboard.json](dashboard.json).

[Grafana Dashboard](https://grafana.com/docs/grafana/latest/dashboards)
of
[Spring Boot](https://spring.io/projects/spring-boot)
(by
[Micrometer](https://micrometer.io)
metrics from
[Prometheus](https://prometheus.io)
) for
[JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity)
and
[HikariCP](https://github.com/brettwooldridge/HikariCP).

## Configure Spring Boot Application

1. Add one of Spring Boot database modules, for example,
   [Spring Data Jpa](https://spring.io/projects/spring-data-jpa)
   or
   [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc).
2. Add
   [Spring Boot Actuator module](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)
   with
   [micrometer-registry-prometheus](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints).
3. Add configuration, for example, `application.yml`:

```yaml
spring:
  application:
    name: myapp
  datasource:
    username: mydb_user
    password: mydb_pass
    url: jdbc:postgresql://localhost:5432/mydb
    hikari:
      minimum-idle: 2
      maximum-pool-size: 8
management:
  endpoints:
    web:
      exposure:
        include:
          - prometheus # Access by /actuator/prometheus
  metrics:
    tags:
      namespace: local # Namespace of k8s
      application: ${spring.application.name}
```

## Configure Prometheus Job

Add job with pulling metrics from `/actuator/prometheus`,
for example, on `localhost:8080`

```yaml
scrape_configs:
  - job_name: myapp-job
    scrape_interval: 15s
    metrics_path: /actuator/prometheus
    static_configs:
      - targets: [ localhost:8080 ]
```
