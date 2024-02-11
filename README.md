# Recogizer DevOps/Cloud Engineer Technical Challenge

## Services Configuration
### Prometheus:

#### Step 1:

Edit existing `docker-compose.yaml` file and add following code for configuring Prometheus with Caddy Service.

```bash
prometheus:
    container_name: prometheus
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
    ports:
      - "9090:9090"
    networks:
      - backend
```
 
**Image**: prom/prometheus:latest is used to pull the latest version of Prometheus.

**Configuration File:** Prometheus is configured via a prometheus.yml file mounted into the container at /etc/prometheus/prometheus.yml. This file defines the scraping rules and targets for Prometheus. Create `prometheus.yaml` in same directory as `docker-compose.yaml`

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'caddy'
    static_configs:
      - targets: ['caddy:2019']
```

*Set Global Scrape Interval:*
The scrape_interval is set to 15s, indicating that Prometheus will collect metrics from your configured targets every 15 seconds.

*Define Scrape Targets:*
Under scrape_configs, define a job named caddy. This job tells Prometheus from where to collect metrics.

*Configure Static Targets:*
Inside the caddy job, use static_configs to specify the actual targets. For this setup, we've defined a single target, caddy:2019, which means Prometheus will scrape metrics from the Caddy server running on port 2019.

**Ports:** The service is exposed on port 9090 of the host machine, allowing you to access the Prometheus UI by navigating to http://localhost:9090.

**Network:** Prometheus is part of the backend network, which allows it to discover and scrape metrics from other services within the same network. Here is the description of prometheus.yaml file which was created under same folder where docker-compose.yaml file is located.

#### Step 2:

To monitor the Caddy server with Prometheus, we must expose a metrics endpoint. The Caddyfile configuration snippet below activates this functionality:

```bash
:2019 {
  # Enable the Prometheus metrics endpoint
  metrics /metrics
}
```
- :2019 specifies that the Caddy server is listening on port 2019. This port is dedicated to exposing metrics data.
- metrics /metrics declares an endpoint on the Caddy server where Prometheus metrics are available. This line configures the Caddy server to serve metrics at the path /metrics on port 2019.


### Grafana

Add following code for configuring Prometheus with Caddy Service.

```bash
grafana:
    container_name: grafana
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    networks:
      - backend
    volumes:
      - grafana_data:/var/lib/grafana

volumes:
  grafana_data:

```

**Image:** grafana/grafana:latest is used to pull the latest version of Grafana.

**Volumes:** A volume named grafana_data is mounted at /var/lib/grafana to persist Grafana data, including dashboards, data sources, and configurations.

**Ports:** The service is exposed on port 3000 of the host machine, allowing you to access the Grafana UI by navigating to http://localhost:3000.

**Network:** Grafana is also part of the backend network, enabling it to access Prometheus as a data source for dashboards.

*Setting Up Prometheus as a Data Source in Grafana*
- Access Grafana: Open your browser and go to http://localhost:3000.
- Default login credentials are usually admin for both username and password, unless changed.
- Add Data Source: Navigate to "Configuration" > "Data Sources" and click on "Add data source".
- Select Prometheus: From the list of available data sources, select "Prometheus".
- Configure Prometheus Data Source: In the URL field, enter the address of the Prometheus service. Since both services are in the backend network, you can use http://prometheus:9090 as the URL.
- Save and Test: Click "Save & Test" to ensure Grafana can communicate with Prometheus.

## Grafana Dashboards:

### 1. Caddy CPU Usage:

![image](https://github.com/FatimaHassan/rg-devops-challenge/assets/26363688/bc482c1e-eb82-4bd7-9cc3-0e7c49a61ca4)

***Query:***
The query `rate(process_cpu_seconds_total[5m])` is used to calculate the per-second average rate of CPU time consumed by the process over the last 5 minutes. 

***Significance:***

- The dashboard provides real-time monitoring of CPU usage, which is critical for performance analysis and capacity planning.
- The data can be used for analyzing trends over time, understanding the load patterns.

### 2. HTTP Request Rate:

![image](https://github.com/FatimaHassan/rg-devops-challenge/assets/26363688/a15fd505-c605-49ec-ab48-5f60e2e4921d)

***Query:***
The metric `rate(caddy_http_request_duration_seconds_count[5m])` is used to calculate the average rate of HTTP requests over a 5-minute window.

***Lines on the Graph:***

There are two lines represented in the graph, differentiated by color and legend:

- Green Line: Represents the rate of requests that are handled directly by Caddy. This indicates successful (code="200") GET requests served by the metrics handler.

- Yellow Line: Shows the rate of requests that are proxied by Caddy to another service. This line indicates successful (code="200") GET requests that were reverse proxied, meaning Caddy is acting as an intermediary for requests to another server or service.

***Significance:***
- This dashboard is useful in identifying Traffic Spikes. The yellow line's spike indicates a sudden increase in proxied requests, which is a result of the makerequest.sh script being run to test the server's response to increased traffic.

## Alerting Rules:

Assuming that server can handle 100 request per second, an alert rule was created to trigger when slightly above the target to record any abnormal behaviour.

![image](https://github.com/FatimaHassan/rg-devops-challenge/assets/26363688/b4e2118d-6794-4c7e-b870-1f135a1283ae)

