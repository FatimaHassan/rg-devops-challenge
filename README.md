# Recogizer DevOps/Cloud Engineer Technical Challenge

## Services Configuration
### Prometheus:

#### Step 1:

Edit existing `docker-compose.yaml` file and add following code for configuring prmetheus with Caddy Service.


  
**Image**: prom/prometheus:latest is used to pull the latest version of Prometheus.

**Configuration File:** Prometheus is configured via a prometheus.yml file mounted into the container at /etc/prometheus/prometheus.yml. This file defines the scraping rules and targets for Prometheus.

**Ports:** The service is exposed on port 9090 of the host machine, allowing you to access the Prometheus UI by navigating to http://localhost:9090.

**Network:** Prometheus is part of the backend network, which allows it to discover and scrape metrics from other services within the same network. Here is the description of prometheus.yaml file which was created under same folder where docker-compose.yaml file is located.

*Set Global Scrape Interval:*
The scrape_interval is set to 15s, indicating that Prometheus will collect metrics from your configured targets every 15 seconds.

*Define Scrape Targets:*
Under scrape_configs, define a job named caddy. This job tells Prometheus from where (which service) to collect metrics.

*Configure Static Targets:*
Inside the caddy job, use static_configs to specify the actual targets. For this setup, we've defined a single target, caddy:2019, which means Prometheus will scrape metrics from the Caddy server running on port 2019.



# Grafana
**Image:** grafana/grafana:latest is used to pull the latest version of Grafana.

**Volumes:** A volume named grafana_data is mounted at /var/lib/grafana to persist Grafana data, including dashboards, data sources, and configurations.
**Ports:** The service is exposed on port 3000 of the host machine, allowing you to access the Grafana UI by navigating to http://<host-ip>:3000.
Network: Grafana is also part of the backend network, enabling it to access Prometheus as a data source for dashboards.
Setting Up Prometheus as a Data Source in Grafana
Access Grafana: Open your browser and go to http://<host-ip>:3000. Default login credentials are usually admin for both username and password, unless changed.
Add Data Source: Navigate to "Configuration" > "Data Sources" and click on "Add data source".
Select Prometheus: From the list of available data sources, select "Prometheus".
Configure Prometheus Data Source: In the URL field, enter the address of the Prometheus service. Since both services are in the backend network, you can use http://prometheus:9090 as the URL.
Save and Test: Click "Save & Test" to ensure Grafana can communicate with Prometheus.
Additional Notes
Customizing Configurations: You might need to customize the prometheus.yml and Grafana data source configurations according to your specific monitoring needs or to add additional services to be monitored.
Network Isolation: The use of a dedicated network (backend) isolates these services from external networks, enhancing security. Ensure that any service you wish to monitor is also attached to this network.

2. **Grafana Dashboards:**
   - Screenshots or snippets of your Grafana dashboards.
   - Explanation of the metrics displayed and their significance.

3. **Alerting Rules:**
   - Description of the alerts you set up in Grafana.
   - Thresholds and conditions for triggering alerts.

4. **Optional Improvements:**
   - If you made enhancements to the Docker Compose file or other components, explain the reasoning behind them.
