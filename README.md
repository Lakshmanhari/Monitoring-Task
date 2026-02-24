# 📊 EC2 Monitoring Setup — Prometheus + Grafana + Node Exporter (Detailed)

---

# 🎯 Project Title

**Cloud Infrastructure Monitoring Using Prometheus and Grafana on AWS EC2**

---

# 🧾 Project Description

This project demonstrates how to deploy a **complete monitoring solution** on a single Linux-based cloud server.
It focuses on collecting system-level metrics such as CPU, memory, disk, and network usage and visualizing them in real-time dashboards.

The monitoring stack is built using industry-standard open-source tools widely used in production environments.

---

# 🎯 Objectives

* Deploy a Linux EC2 instance in AWS
* Install and configure Node Exporter to collect system metrics
* Configure Prometheus to scrape and store metrics
* Connect Grafana to Prometheus
* Create dashboards for infrastructure monitoring

---

# 🧰 Tools & Technologies

* AWS EC2 (Ubuntu Linux)
* Prometheus (Monitoring & Time-Series Database)
* Grafana (Visualization Platform)
* Node Exporter (System Metrics Collector)

---

# 🧱 Architecture Overview

```
EC2 Instance (Ubuntu)

System Metrics (CPU / Memory / Disk)
            │
            ▼
Node Exporter (Port 9100)
            │
            ▼
Prometheus (Port 9090)
            │
            ▼
Grafana (Port 3000)
            │
            ▼
User Web Browser
```

---

# 🔍 Architecture Explanation

1️⃣ The Linux operating system continuously generates performance metrics.
2️⃣ Node Exporter collects these metrics and exposes them via an HTTP endpoint.
3️⃣ Prometheus scrapes this endpoint at regular intervals and stores the data.
4️⃣ Grafana queries Prometheus to retrieve metrics.
5️⃣ Users view the data through interactive dashboards in the browser.

---

# 🪜 Implementation Steps

---

## ✅ Step 1 — Launch EC2 Instance

1. Open AWS Console → EC2 → Launch Instance
2. Select **Ubuntu 22.04 LTS**
3. Choose **t3.micro** instance type
4. Configure security group

### Open Ports

* 22 → SSH
* 9090 → Prometheus
* 3000 → Grafana
* 9100 → Node Exporter

Connect to instance

```bash
ssh -i key.pem ubuntu@<PUBLIC-IP>
```

---

## ✅ Step 2 — Install Node Exporter

Node Exporter collects OS-level metrics including CPU load, memory usage, disk utilization, and network statistics.

```bash
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.10.2.linux-amd64.tar.gz

tar xvf node_exporter-*.tar.gz

sudo cp node_exporter-*/node_exporter /usr/local/bin/

nohup node_exporter &
```

### Verification

Open in browser

```
http://<EC2-IP>:9100/metrics
```

You should see raw metrics output.

---

## ✅ Step 3 — Install Prometheus

Prometheus is responsible for scraping and storing metrics as time-series data.

```bash
wget https://github.com/prometheus/prometheus/releases/download/v3.9.1/prometheus-3.9.1.linux-amd64.tar.gz

tar xvf prometheus-*.tar.gz

cd prometheus-*
```

### Configure Prometheus

Edit configuration

```bash
nano prometheus.yml
```

Paste

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
```

### Start Prometheus

```bash
./prometheus --config.file=prometheus.yml
```

Open

```
http://<EC2-IP>:9090
```

Check → Status → Targets → **UP**

---

## ✅ Step 4 — Install Grafana

Grafana provides visualization and dashboard capabilities.

```bash
sudo apt update
sudo apt-get install -y adduser libfontconfig1 musl

wget https://dl.grafana.com/grafana-enterprise/release/12.4.0/grafana-enterprise_12.4.0_22325204712_linux_amd64.deb

sudo dpkg -i grafana-enterprise_12.4.0_22325204712_linux_amd64.deb
```

Start service

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Open

```
http://<EC2-IP>:3000
```

Login

```
admin / admin
```

---

## ✅ Step 5 — Add Prometheus as Data Source

1. Open Grafana
2. Go to Settings → Data Sources
3. Click Add Data Source → Prometheus
4. Enter

```
http://localhost:9090
```

Click **Save & Test**

---

## ✅ Step 6 — Create Monitoring Dashboard

1. Click ➕ → Dashboard → Add Panel
2. Choose Prometheus as data source

### Example Queries

CPU

```
node_cpu_seconds_total
```

Memory

```
node_memory_MemAvailable_bytes
```

Disk

```
node_filesystem_avail_bytes
```

Save dashboard

```
EC2 Monitoring Dashboard
```

---

# 📊 Expected Results

✔ Metrics visible in Prometheus
✔ Targets status showing UP
✔ Grafana dashboard displaying graphs
✔ Real-time system monitoring

---

# 🗣️ Viva Explanation

This project implements a monitoring pipeline where Node Exporter collects system metrics from the EC2 instance. Prometheus scrapes and stores these metrics in a time-series database. Grafana connects to Prometheus and visualizes the metrics using dashboards. This architecture represents a real-world observability setup used in production environments.

---

# 🚀 Future Enhancements

* Configure alerting rules
* Add email or Slack notifications
* Monitor multiple servers
* Integrate log monitoring (Loki)
* Containerize using Docker

---

# 📌 Conclusion

The project successfully demonstrates the deployment of a complete monitoring stack on AWS. It provides practical exposure to cloud infrastructure monitoring and highlights how open-source tools can be integrated to build scalable observability solutions.

---
