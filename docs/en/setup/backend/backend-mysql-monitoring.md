# MySQL monitoring
SkyWalking leverages prometheus/mysqld_exporter for collecting metrics data from MySQL. It leverages OpenTelemetry Collector to transfer the metrics to
[OpenTelemetry receiver](opentelemetry-receiver.md) and into the [Meter System](./../../concepts-and-designs/meter.md).

## Data flow
1. mysqld_exporter collect metrics data from MySQL.
2. OpenTelemetry Collector fetches metrics from mysqld_exporter via Prometheus Receiver and pushes metrics to SkyWalking OAP Server via the OpenCensus GRPC Exporter.
3. The SkyWalking OAP Server parses the expression with [MAL](../../concepts-and-designs/mal.md) to filter/calculate/aggregate and store the results.

## Set up
1. Set up [mysqld_exporter](https://github.com/prometheus/mysqld_exporter#using-docker).
2. Set up [OpenTelemetry Collector ](https://opentelemetry.io/docs/collector/getting-started/#docker). For details on Prometheus Receiver in OpenTelemetry Collector, refer to [here](../../../../test/e2e-v2/cases/mysql/prometheus-mysql-exporter/otel-collector-config.yaml).
3. Config SkyWalking [OpenTelemetry receiver](opentelemetry-receiver.md).

## MySQL Monitoring
MySQL monitoring provides monitoring of the status and resources of the MySQL server. MySQL server as a `Service` in OAP, and land on the `Layer: MYSQL`.

### MySQL Supported Metrics
| Monitoring Panel | Unit | Metric Name | Description | Data Source |
|-----|------|-----|-----|-----|
| MySQL Uptime |   day   | meter_mysql_uptime | The MySQL startup time | mysqld_exporter|
| Max Connections |      | meter_mysql_max_connections | The max number of connections. | mysqld_exporter|
| Innodb Buffer Pool Size |  MB    | meter_mysql_innodb_buffer_pool_size | The buffer pool size in Innodb engine | mysqld_exporter|
| Thread Cache Size |      | meter_mysql_thread_cache_size | The size of thread cache | mysqld_exporter|
| Current QPS|      | meter_mysql_qps | Queries Per Second | mysqld_exporter|
| Current TPS |      | meter_mysql_tps | Transactions Per Second | mysqld_exporter|
| Commands Rate |     | meter_mysql_commands_insert_rate <br/>meter_mysql_commands_select_rate<br />meter_mysql_commands_delete_rate<br />meter_mysql_commands_update_rate | The rate of total number of insert/select/delete/update executed by the current server | mysqld_exporter|
| Threads |    | meter_mysql_threads_connected<br />meter_mysql_threads_created<br />meter_mysql_threads_cached<br />meter_mysql_threads_running | The number of currently open connections(threads_connected) <br/> The number of threads created(threads_created) <br/> The number of threads in the thread cache(threads_cached) <br/> The number of threads that are not sleeping(threads_running) | mysqld_exporter|
| Connects |    | meter_mysql_connects_available<br />meter_mysql_connects_aborted | The number of available connections(connects_available)<br/>The number of MySQL instance connection rejections(connects_aborted)| mysqld_exporter|
| Connection Errors |      | meter_mysql_connection_errors_internal </br> meter_mysql_connection_errors_max_connections | Errors due to exceeding the max_connections(connection_errors_max_connections) </br>Error caused by internal system(connection_errors_internal) | mysqld_exporter|
| Slow Queries Rate |      | meter_mysql_slow_queries_rate | The rate of slow queries  | mysqld_exporter|


## Customizations
You can customize your own metrics/expression/dashboard panel.
The metrics definition and expression rules are found in `/config/otel-rules/mysql.yaml`.
The MySQL dashboard panel configurations are found in `/config/ui-initialized-templates/mysql`.
