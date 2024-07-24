\# Setup a Monitoring tools for DevOps

\> \*\*Deployment Documentation for Grafana and Prometheus\*\*

\## Overview

This documentation provides a step-by-step guide for deploying Grafana
and Prometheus on a server and configuring email alerts.

\## System Requirements

\- \*\*Server IP:\*\* 103.151.111.237

\- \*\*Operating System:\*\* Ubuntu 24.04

\## 1. Deploying Prometheus

\### 1.1 Install Prometheus

\*\*Download Prometheus:\*\*

\`\`\`sh

wget
<https://github.com/prometheus/prometheus/releases/download/v2.43.0/prometheus-2.43.0.linux-amd64.tar.gz>\
\
**Extract the Archive:**

tar -xvzf prometheus-2.43.0.linux-amd64.tar.gz

cd prometheus-2.43.0.linux-amd64

**Move Binaries:**

sudo mv prometheus /usr/local/bin/

sudo mv promtool /usr/local/bin/

**Create Prometheus Configuration Directory:**

sudo mkdir /etc/prometheus

**Move Configuration Files:**

sudo mv prometheus.yml /etc/prometheus/

**Create Prometheus Service File:**

sudo nano /etc/systemd/system/prometheus.service

Add the following content:

\[Unit\]

Description=Prometheus

Wants=network-online.target

After=network-online.target

\[Service\]

User=root

ExecStart=/usr/local/bin/prometheus
\--config.file=/etc/prometheus/prometheus.yml

\[Install\]

WantedBy=multi-user.target

**Start and Enable Prometheus Service:**

sudo systemctl daemon-reload

sudo systemctl start prometheus

sudo systemctl enable prometheus

### 1.2 Configure Prometheus

****Edit Configuration File:****

sudo nano /etc/prometheus/prometheus.yml

Example configuration:

global:

scrape_interval: 15s

scrape_configs:

 - job_name: \'node_exporter\'

static_configs:

\- targets: \[\'localhost:9100\'\]

**Verify Prometheus is Running:**

Open your browser and go to
[http://103.151.111.237:9090](http://103.151.111.237:9090/).

## 2. Deploying Grafana

### 2.1 Install Grafana

**Add Grafana APT Repository:**

sudo apt-get install -y software-properties-common

sudo add-apt-repository \"deb https://packages.grafana.com/oss/deb
stable main\"

**Add Grafana GPG Key:**

wget -q -O - https://packages.grafana.com/gpg.key \| sudo apt-key add -

**Install Grafana:**

sudo apt-get update

sudo apt-get install grafana

**Start and Enable Grafana Service:**

sudo systemctl start grafana-server

sudo systemctl enable grafana-server

### 2.2 Configure Grafana

****Access Grafana:****

****Open your browser and go to
****[****http://103.151.111.237:3000****](http://103.151.111.237:3000/)****.\
Default Credentials: *****admin/admin*****

**Add Prometheus Data Source:**

1.  Navigate to Configuration -\> Data Sources.
2.  Click Add data source.
3.  Choose Prometheus from the list.
4.  Set the URL to *http://localhost:9090*.
5.  Click Save & Test.

**Create Dashboards and Panels:**

1.  Go to + -\> Create -\> Dashboard.
2.  Click Add new panel.
3.  Configure panels with Prometheus queries.

## ****3. Configure Email Alerts****

### ****3.1 Set Up Email Contact Points****

**Navigate to Contact Points:**

1.  ****In Grafana, go to Alerting -\> Contact points.****
2.  Click New contact point.

**Configure Email Contact Point:**

-   **Name:** Enter a name (e.g., \"Email Alerts\").
-   **Type:** Select Email.
-   **Email addresses:** Enter the email address(es) to receive alerts.
-   **Save Contact Point:** Click Save contact point.

### 3.2 Set Up Notification Policies

**Navigate to Notification Policies:**

1.  Go to Alerting -\> Notification policies.

**Add a New Child Policy:**

Click New child policy.

1.  **Group:** Choose the alert group or keep it as Default.
2.  **Contact Point:** Select the contact point you created (e.g.,
    \"Email Alerts\").
3.  **Conditions:** Configure conditions as needed.
4.  **Save Policy:** Click Save child policy.

### 3.3 Create Alert Rules

**Navigate to Your Dashboard:**

1.  Go to the dashboard where you want to add alerts.

**Add an Alert to a Panel:**

1.  Click on the panel you want to set an alert for.
2.  Click the bell icon or go to Alert -\> Create alert rule.

**Configure the Alert Rule:**

-   **Name:** Enter a name for the alert rule.

-   **Condition:** Define the condition for the alert (e.g., when a
    metric exceeds a threshold).

-   **Expression:** For example,
    *rate(node_cpu_seconds_total{mode=\"system\"}\[5m\]) \> 0.8*

-   **Condition:** IS ABOVE 80 (for CPU usage).

-   **Evaluation:** Set how frequently the alert should be evaluated.

    -   **Evaluate every:** 1m (1 minute)
    -   **For:** 5m (5 minutes)

-   **Notifications:** Choose the notification policy you created (e.g.,
    \"Email Alerts\").

-   **Save Alert Rule:** Click Save.
