# Comprehensive Guide to Setting up Grafana, Prometheus, and Loki

## Why I Wrote This Guide
When I first started learning Grafana and Prometheus, I quickly realized that setting up these tools is not a one-size-fits-all process. Each environment—whether it's a traditional data center, Docker, or Kubernetes—presents unique challenges in configuration, integration, and optimization. There was a lack of comprehensive, step-by-step guides that addressed these nuances across different infrastructures, and I often found myself piecing together information from multiple sources.

This guide aims to be a one-stop resource for anyone looking to set up Grafana, Prometheus, and Loki in various environments. It includes practical examples, detailed configuration steps, and insights to help both beginners and experienced users build a robust monitoring and logging solution tailored to their specific setup. My goal is to make the learning curve smoother and help others avoid the challenges I faced along the way.

---

### Connect with me:
- [LinkedIn](https://www.linkedin.com/in/asif-muzammil-hussain-b6742441/)
- [UpWork](https://www.upwork.com/freelancers/~01502e59c2c0439036?mp_source=share)
- [Fiverr](https://www.fiverr.com/s/jjg3zDV)
---

## 1. Introduction to Grafana
If you've worked with monitoring tools, you've probably heard of Grafana. It's one of the most popular platforms for creating rich, interactive dashboards to visualize your data. Whether you're tracking application performance, server health, or infrastructure usage, Grafana helps you make sense of it all.

### 1.1 What Exactly is Grafana?
Grafana is an open-source analytics and monitoring platform. It allows you to create beautiful, real-time dashboards from various data sources. In this blog, we’ll be focusing on Grafana version 9.3 and above, as these newer versions bring several enhancements, such as improved user experience, better alerting, and additional data source support.

#### Why use Grafana?
- **Ease of use:** Grafana’s intuitive interface makes creating dashboards simple, even for beginners.
- **Multi-source support:** Grafana can pull in data from Prometheus, MySQL, Loki, CloudWatch, and many other sources.
- **Real-time monitoring:** Version 9.3+ adds improvements in live dashboards and real-time alerting features.
- **Customization:** Grafana is highly customizable with plugins, themes, and integrations.
- **Improved alerting:** In versions 9.3 and later, alert management has become more flexible with features like multi-dimensional alerts and better notification systems.

### 1.2 What Can You Do with Grafana?
With Grafana, you can:
- **Create custom dashboards:** These can be tailored to visualize data from multiple sources in real-time.
- **Connect different data sources:** We’ll explore data sources like Prometheus, Loki, MySQL, and more.
- **Integrate third-party services:** Grafana’s plugin ecosystem allows you to integrate with popular tools like Slack, Jira, and Azure DevOps.
- **Set up advanced alerting:** Since version 9.3, alerts have become more powerful with new templating options and alerting rules.

**Note:** If you're working with older versions of Grafana, certain features like the enhanced alerting options and the new user interface might not be available.

Here's the content converted into Markdown format:

```markdown
## 2. Understanding Grafana Data Sources
Grafana’s real power comes from its ability to connect to multiple data sources. Once you've got your data flowing in, Grafana helps you visualize it. Let’s take a look at some of the most commonly used data sources.

### 2.1 Prometheus (Metrics)
Prometheus is one of the most widely used monitoring systems. It collects metrics from your applications, servers, or infrastructure, and works especially well with Grafana to create powerful dashboards.

#### How to integrate Prometheus with Grafana:
Once you have Prometheus running, add it as a data source in Grafana by navigating to `Configuration > Data Sources`. Select Prometheus and enter the URL where Prometheus is running (e.g., `http://localhost:9090`).

#### Sample PromQL queries:
- **CPU usage for all instances:**
  ```promql
  sum(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)
  ```

- **Memory usage:**
  ```promql
  node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100
  ```

**Additional Resource:** Looking for pre-built Prometheus alerting rules? Check out this [Awesome Prometheus Alerts page](#) for a comprehensive collection of alerting rules tailored for Kubernetes environments.

[Need more details? Visit the official Prometheus Integration in Grafana](#).

---

### 2.2 Loki (Logs)
Loki is Grafana’s go-to tool for managing logs. It’s designed to be lightweight and works seamlessly alongside Prometheus, but while Prometheus focuses on metrics, Loki is used for logs.

#### How to integrate Loki:
Add Loki as a data source by selecting `Configuration > Data Sources` in Grafana, choose Loki, and provide the URL where Loki is running (e.g., `http://localhost:3100`).

#### Sample LogQL queries:
- **Query logs for the keyword "error" in a specific job:**
  ```logql
  {job="grafana"} |= "error"
  ```

Find more in the [Loki documentation](#).

---

### 2.3 CloudWatch (AWS Monitoring)
If you're running infrastructure on AWS, connecting CloudWatch to Grafana is a must. This integration lets you monitor your AWS resources like EC2, Lambda, and RDS, all from Grafana.

#### How to integrate CloudWatch:
In Grafana, go to `Data Sources > Add Data Source` and choose CloudWatch. You'll need to enter your AWS credentials or set up access through IAM roles.

Check out the [CloudWatch Integration documentation](#) for more.

---

### 2.4 MySQL (Relational Databases)
Grafana can also pull data from relational databases like MySQL, allowing you to visualize query results directly from your database tables.

#### How to integrate MySQL:
Add MySQL as a data source by providing your host, port, and credentials. You can then run SQL queries directly in Grafana to visualize your data.

#### Sample SQL query:
```sql
SELECT
    time AS "time",
    value
FROM
    metrics_table
WHERE
    $__timeFilter(time)
```

More details: [MySQL Integration](https://grafana.com/docs/grafana/latest/datasources/mysql).
Here’s the content converted into Markdown format:

```markdown
## 3. Setting up Grafana with Middleware (Prometheus, Loki) in Different Environments
Grafana works best when paired with middleware like Prometheus (for metrics) and Loki (for logs). In this section, we’ll install and configure Grafana with these middleware in various environments, giving you a strong foundation for monitoring.

### 3.1 Configuration of Grafana In Traditional Data Center
#### Setting up Grafana and Middleware in a Traditional Data Center (on Physical Servers)
In a traditional data center setup, you can run Grafana alongside Prometheus and Loki on physical Linux servers. This setup gives you full control over both hardware and software, but scaling may require manual intervention.

---

#### 3.1.1 Server Preparation in a Traditional Data Center
Ensure your server runs a supported operating system (e.g., Ubuntu, Debian, CentOS).

For Ubuntu/Debian:
```bash
sudo apt update && sudo apt upgrade -y
```

For CentOS:
```bash
sudo yum update -y
```

##### For Setup on Ubuntu:
```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt update && sudo apt install grafana -y
```

##### For CentOS:
```bash
sudo yum install -y https://packages.grafana.com/oss/rpm/grafana-8.0.0-1.x86_64.rpm
```

##### Start Grafana:
```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

##### Access Grafana UI:
Adjust firewall settings to allow traffic on Grafana’s default port (3000). Open a web browser and go to `http://<your_server_ip>:3000`. Log in with the default credentials (`admin / admin`).

---

### 3.1.2 Guide for Setting up Prometheus Including Node Exporter as a Service
##### Create a Dedicated User for Prometheus & Node Exporter:
```bash
useradd --no-create-home --shell /bin/false prometheus
useradd --no-create-home --shell /bin/false node_exporter
```

##### Create Required Directories:
```bash
mkdir /etc/prometheus
mkdir /var/lib/prometheus
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

##### Download and Install Prometheus:
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.41.0/prometheus-2.41.0.linux-amd64.tar.gz
tar -xzvf prometheus-2.41.0.linux-amd64.tar.gz
cp prometheus-2.41.0.linux-amd64/prometheus /usr/local/bin/
cp prometheus-2.41.0.linux-amd64/promtool /usr/local/bin/
```

##### Create a Systemd Service for Prometheus:
```bash
sudo nano /etc/systemd/system/prometheus.service
```

Add the following content to the file:
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

##### Copy Configuration Files and Libraries:
```bash
cp -r prometheus-2.41.0.linux-amd64/consoles /etc/prometheus
cp -r prometheus-2.41.0.linux-amd64/console_libraries /etc/prometheus
cp prometheus-2.41.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
chown -R prometheus:prometheus /etc/prometheus
```

##### Install Node Exporter as a Service:
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
tar -xzvf node_exporter-1.5.0.linux-amd64.tar.gz
cp node_exporter-1.5.0.linux-amd64/node_exporter /usr/local/bin/
```

##### Create a Systemd Service for Node Exporter:
```bash
sudo nano /etc/systemd/system/node_exporter.service
```

Add the following content to the file:
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

##### Configure Prometheus to Scrape Node Exporter:
```bash
sudo nano /etc/prometheus/prometheus.yml
```

Add the following configuration:
```yaml
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

##### Start and Enable Prometheus & Node Exporter:
```bash
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
systemctl start prometheus
systemctl enable prometheus
```

---

### 3.1.3 Guide for Setting up Loki (for Logs) Including Promtail as a Service
##### Download and Install Loki:
```bash
wget https://github.com/grafana/loki/releases/download/v2.8.0/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
chmod +x loki-linux-amd64
sudo mv loki-linux-amd64 /usr/local/bin/loki
```

##### Create a Configuration File for Loki:
```bash
sudo nano /etc/loki/loki-config.yaml
```

##### Create a Systemd Service for Loki:
```bash
sudo nano /etc/systemd/system/loki.service
```

##### Download and Install Promtail:
```bash
wget https://github.com/grafana/loki/releases/download/v2.8.0/promtail-linux-amd64.zip
unzip promtail-linux-amd64.zip
chmod +x promtail-linux-amd64
sudo mv promtail-linux-amd64 /usr/local/bin/promtail
```
```
