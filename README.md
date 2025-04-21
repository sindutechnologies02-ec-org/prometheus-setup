# Prometheus + Node Exporter + Grafana Setup

This project sets up Prometheus, Node Exporter, and Grafana on an EC2 instance (Amazon Linux 2). The goal is to collect system metrics like CPU, memory, disk, and network usage using Prometheus, and visualize the data with Grafana.

## Project Overview

- **Prometheus**: Collects and stores system metrics.
- **Node Exporter**: Exports system metrics for Prometheus.
- **Grafana**: Visualizes the metrics collected by Prometheus in beautiful dashboards.

## Prerequisites

- An EC2 instance running Amazon Linux 2 (t2.micro or larger).
- Public IP of the EC2 instance for accessing Prometheus (port 9090) and Grafana (port 3000).
- Security Group configured to allow inbound traffic on ports:
  - 9090 for Prometheus
  - 3000 for Grafana

## Step-by-Step Setup

### Step 1: Launch EC2 Instance & Connect via SSH

1. Launch an EC2 instance with **Amazon Linux 2**.
2. Allow inbound traffic for ports 9090 (Prometheus) and 3000 (Grafana) in the Security Group.
3. Connect to the EC2 instance using **PuTTY** or **SSH**.

### Step 2: Install Prometheus

1. Create necessary directories for Prometheus:
   ```bash
   sudo useradd --no-create-home --shell /bin/false prometheus
   sudo mkdir /etc/prometheus
   sudo mkdir /var/lib/prometheus
Download and install Prometheus:
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.48.1/prometheus-2.48.1.linux-amd64.tar.gz
tar xvf prometheus-2.48.1.linux-amd64.tar.gz
cd prometheus-2.48.1.linux-amd64
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus
sudo cp prometheus.yml /etc/prometheus/
Create the Prometheus systemd service to manage Prometheus as a service:
sudo nano /etc/systemd/system/prometheus.service
Paste the following configuration:
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/usr/local/bin/prometheus \
  --config.file /etc/prometheus/prometheus.yml \
  --storage.tsdb.path /var/lib/prometheus/

[Install]
WantedBy=default.target
Reload the systemd configuration and start Prometheus:
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
Prometheus should now be accessible at http://<EC2-PUBLIC-IP>:9090
step 3.: Install Node Exporter
1.Download and install Node Exporter:
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
tar xvf node_exporter-1.7.0.linux-amd64.tar.gz
cd node_exporter-1.7.0.linux-amd64
sudo cp node_exporter /usr/local/bin/

2.Create the Node Exporter systemd service:
sudo nano /etc/systemd/system/node_exporter.service
Paste the following configuration:
[Unit]
Description=Node Exporter

[Service]
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=default.target
3.Start Node Exporter:
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
Node Exporter will now be available at http://<EC2-PUBLIC-IP>:9100/metrics.

step 4:Add Node Exporter to Prometheus
1.Edit Prometheus configuration:
sudo nano /etc/prometheus/prometheus.yml
2.Add the Node Exporter job under the scrape_configs section:
- job_name: 'node_exporter'
  static_configs:
    - targets: ['localhost:9100']
3.Restart Prometheus:
sudo systemctl restart prometheus
Prometheus is now scraping metrics from Node Exporter.

Step 5: Install Grafana
Install Grafana:
sudo yum install -y https://dl.grafana.com/oss/release/grafana-10.2.3-1.x86_64.rpm
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
Grafana will now be available at http://<EC2-PUBLIC-IP>:3000.

Login to Grafana:

Username: admin

Password: admin (you will be prompted to change it upon first login).

Step 6: Add Prometheus as a Data Source in Grafana
In Grafana, go to Settings > Data Sources.

Click Add data source and select Prometheus.

In the URL field, enter http://localhost:9090.

Click Save & Test.

Step 7: Import Node Exporter Dashboard
In Grafana, go to Dashboards > Import.

Enter the dashboard ID 1860 (Node Exporter Full).

Click Import to load the dashboard.

Step 8: Visualize Metrics
Now, you can view various metrics like CPU, memory, disk, and network usage on the Grafana dashboard!

Conclusion
You've successfully set up Prometheus, Node Exporter, and Grafana to monitor your EC2 instance's system metrics. With Grafana's visualizations, you can monitor the health and performance of your system in real-time.

Screenshots
![image](https://github.com/user-attachments/assets/b11a31b6-2182-4886-b408-f74e4083d1f6)
![image](https://github.com/user-attachments/assets/4fb1b611-c6e5-4665-b489-d2484b3142fc)
![image](https://github.com/user-attachments/assets/90259d59-c529-4f63-8fbe-d4e1f337a338)
![image](https://github.com/user-attachments/assets/d1464632-230a-4980-9e60-42532bdf8cbc)



