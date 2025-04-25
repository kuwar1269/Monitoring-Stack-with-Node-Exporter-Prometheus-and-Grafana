
# **Monitoring Stack with Node Exporter, Prometheus, and Grafana** 📊

## **Overview** 🌍

This project sets up a monitoring stack using **Node Exporter**, **Prometheus**, and **Grafana** to collect and visualize system metrics from a Linux machine (or WSL) using Docker Compose.

### **Technologies Used:** 🛠️
- **Node Exporter**: Collects system metrics. 📈
- **Prometheus**: Scrapes and stores metrics. 🧑‍💻
- **Grafana**: Visualizes metrics in dashboards. 📉

---

## **Prerequisites** ⚙️

- **Docker** and **Docker Compose** installed.  
  If not, follow the [Docker installation guide](https://docs.docker.com/get-docker/). 🐋
- **WSL 2 (Windows Subsystem for Linux)** if running on Windows with Ubuntu.  
  [WSL Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install). 🖥️
- **Basic knowledge** of Docker, Prometheus, and Grafana. 📚

---

## **Setup Instructions** 🛠️

### **1. Install Node Exporter on Linux/WSL** 🔧

Node Exporter collects system metrics and exposes them for Prometheus.

1. **Download and Extract Node Exporter**:
   ```bash
   wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
   tar xvfz node_exporter-1.9.1.linux-amd64.tar.gz
   cd node_exporter-1.9.1.linux-amd64
   ```
   ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img1.png)
   ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img2.png)
   ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img3.png)

3. **Run Node Exporter**:
   ```bash
   ./node_exporter
   ```
   - Node Exporter will be running on `http://localhost:9100`. 🌐
   - ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img4.png)

4. **Verify Metrics**:
   - Open [http://localhost:9100/metrics](http://localhost:9100/metrics) in your browser. 🌐
   - Or use `curl` to verify:
     ```bash
     curl http://localhost:9100/metrics
     
     ```
     ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img5.png)

5. **Note the WSL IP (if needed)**:
   If Prometheus is outside WSL, get the WSL IP using:
   ```bash
   hostname -I
   ```
   Replace `localhost` with this IP in `prometheus.yml` later.

---

### **2. Set Up Prometheus & Grafana with Docker Compose** 🚢

Prometheus scrapes metrics from Node Exporter and Grafana visualizes them.

1. **Create `docker-compose.yml`**:
   In your project directory, create the file `docker-compose.yml` with the following content:
   ```yaml
   version: '3'
   services:
     prometheus:
       image: prom/prometheus
       ports:
         - "9090:9090"
       volumes:
         - ./prometheus.yml:/etc/prometheus/prometheus.yml
     grafana:
       image: grafana/grafana
       ports:
         - "3000:3000"
   ```

2. **Configure `prometheus.yml`**:
   Create the file `prometheus.yml` with the following content:
   ```yaml
   global:
     scrape_interval: 15s
   scrape_configs:
     - job_name: 'node-exporter'
       static_configs:
         - targets: ['<WSL_IP>:9100']  # Replace with WSL IP if Prometheus is outside WSL
   ```

3. **Start Services**:
   Run the following command to start Prometheus and Grafana with Docker Compose:
   ```bash
   docker-compose up -d
   ```
![IMG](![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/img6.png))
4. **Verify Prometheus**:
   - Open [http://localhost:9090](http://localhost:9090) in your browser. 🌐
   - Check that Node Exporter is `UP` under **Status → Targets**. ✅

---

### **3. Set Up Grafana Dashboard** 📊

Grafana provides the visualization layer for metrics collected by Prometheus.

1. **Access Grafana**:
   Open [http://localhost:3000](http://localhost:3000) in your browser. 🌐
   - Default login: `admin` / `admin`
![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/Grafana%20dashboard.png)
2. **Add Prometheus Data Source**:
   - Go to **Configuration → Data Sources → Add Prometheus**.
   - Set the URL to `http://prometheus:9090` (or use `http://<Prometheus_IP>:9090` if Prometheus is not running in Docker).
   - ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/prometheus.png)

3. **Import Node Exporter Dashboard**:
   - Go to **Create (+) → Import → Dashboard ID `1860`**
     ![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/import.png)
     (or download the [Node Exporter Full Dashboard](http://localhost:3000/d/rYdddlPWk/node-exporter-full?orgId=1&from=now-24h&to=now&timezone=browser&var-datasource=default&var-job=&var-nodename=&var-node=&var-diskdevices=%5Ba-z%5D%2B%7Cnvme%5B0-9%5D%2Bn%5B0-9%5D%2B%7Cmmcblk%5B0-9%5D%2B&refresh=1m)).
   - Select **Prometheus** as the data source and click **Import**.

4. **View Metrics**:
   The imported dashboard will display CPU, memory, disk, and network metrics. 📉
![IMG](https://github.com/divyanshsaxena002/Monitoring-Stack-with-Node-Exporter-Prometheus-and-Grafana/blob/main/img/import1.png)
---

## **Troubleshooting** ⚠️

- **Docker Integration with WSL**: If running on Windows with WSL, make sure Docker Desktop is integrated with your WSL distro. Follow the [Docker WSL2 guide](https://docs.docker.com/go/wsl2/) for setup. 🖥️
- If you run Prometheus outside WSL, make sure to replace `localhost` with the WSL IP in the `prometheus.yml` file. 📡
