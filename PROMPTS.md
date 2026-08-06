Prompt to inspect users, service accounts and dashboards

```
Using the connected Grafana MCP config, please inspect my Grafana instance and report:

1. Grafana users
   - List visible organization users.
   - Include login, name, email, and role where available.
   - If global user lookup is blocked, mention the missing permission and fall back to org users.

2. Service accounts
   - List all service accounts.
   - Include name, login, role, enabled/disabled status, and token count where available.

3. Datasources
   - List all configured datasources.
   - Include name, UID, type, URL, and whether it is the default datasource.
   - Run a health check for each datasource if supported.

Please summarize the totals for users, service accounts, and datasources at the top, then provide the detailed lists below.
```

Prompt to create redis and java spring boot dashboard. 

```


Using the connected Grafana MCP config, please configure Grafana monitoring for my local Docker-based stack.

Tasks:

1. Check the available Grafana instance via MCP.
2. Create or update a Prometheus datasource named `prometheus`.
   - Prometheus is running in Docker.
   - Use the Docker-internal URL `http://prometheus:9090`.
   - Set it as the default datasource.
   - Verify the datasource health after creation.
3. Create a Grafana folder named `Application Monitoring`.
4. Create a Redis dashboard aligned to the `prometheus` datasource.
   - Assume Redis exporter is running and Prometheus is scraping it.
   - Include panels for:
     - Redis availability
     - Memory usage
     - Connected and blocked clients
     - Command throughput
     - Network input/output
     - Cache hit ratio
     - Keys and expiring keys by database
     - Expired keys, evicted keys, and rejected connections
     - Replication health
     - Persistence/fork timing
     - Slowlog length
   - Add useful variables for `job` and `instance`.
5. Create a Spring Boot application dashboard aligned to the same `prometheus` datasource.
   - Assume the app will later expose standard Spring Boot Actuator / Micrometer Prometheus metrics.
   - Include panels for:
     - App availability
     - HTTP request rate
     - HTTP 4xx/5xx errors
     - HTTP latency p95/p99
     - JVM heap and non-heap memory
     - JVM memory pools
     - GC pause count and duration
     - JVM threads
     - CPU usage
     - Loaded classes
     - Tomcat threads and sessions
     - HikariCP pool usage
     - Logback events
     - Process uptime and open files
   - Add useful variables for `job` and `instance`.
6. After creating everything, verify the dashboards exist and report:
   - Datasource name, UID, and health
   - Dashboard names and URLs
   - Any assumptions made about metric names
```

Clean all the resources we created by above prompt. 

```
Using the connected Grafana MCP config, delete only the Grafana resources that were created by our earlier setup prompt/session.

Before deleting:
1. Re-list dashboards, folders, and datasources.
2. Identify only resources that clearly match what we previously created.
3. Do not delete shared, production-looking, default, or pre-existing resources unless they are explicitly listed below.

Approved for deletion only if they still exist:
- Dashboard: Redis Exporter Overview, UID `redis-exporter-overview`
- Dashboard: Spring Boot Application Overview, UID `spring-boot-micrometer-overview`
- Folder: Application Monitoring, UID `app-monitoring`, but only after its dashboards are deleted and it is empty.
- Datasource: prometheus which is defalut datasource. 

Deletion order:
1. Delete the approved dashboards by UID.
2. Verify the folder is empty.
3. Delete the approved folder by UID only if empty.
4. Re-list dashboards, folders, and datasources.
5. Report deleted, skipped, failed, and remaining resources.

If any permission is missing, stop and report the required Grafana permission.
```