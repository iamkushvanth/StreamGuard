# StreamGuard 🛡️📡  
*Real-Time Anti-Piracy Analytics for Live Sports Streaming*

## 🚀 Project Objective

**StreamGuard** is a data-driven anti-piracy solution designed to detect, analyze, and prioritize pirated live sports streams—especially during high-stakes events. It provides actionable insights to help sports streaming platforms protect their content and revenue.

---

## 🧠 How It Works

StreamGuard uses web scraping and real-time Kafka processing on an Azure Virtual Machine (VM) to collect and store piracy links and evidence. It focuses on specific sporting events (e.g., "Indian Premier League") and stores evidence like screenshots and iframe sources in Azure Blob Storage.

---

## ⚙️ Tech Stack

- **Apache Kafka** – Real-time data pipeline (hosted on Azure VM)
- **Azure VM (Ubuntu 20.04 LTS)** – Runs Kafka and scripts
- **Azure Blob Storage** – Stores links, screenshots, and iframe metadata
- **Python** – Core logic  
  - `kafka-python` for Kafka producers/consumers  
  - `azure-storage-blob` for Blob uploads  
  - `selenium` for scraping & evidence collection  
- **Selenium (Headless Chrome)** – Automates evidence gathering  
- **CLI Execution** – All scripts run on Azure VM via command line

---

## 🏗️ Architecture Overview

1. **Producer**
   - Scrapes websites for piracy links related to specific events.
   - Captures screenshots and iframe sources.
   - Publishes JSON messages to Kafka topics (`piracy-links`, `piracy-evidence`).

2. **Consumers**
   - `Links Consumer`: Saves link data to `streamguard-links` container.
   - `Evidence Consumer`: Uploads screenshots and iframe metadata to `streamguard-evidence`.

3. **Storage**
   - **Blob Containers**:
     - `streamguard-links` for JSON link files
     - `streamguard-evidence` for screenshots and iframe JSON

---

## 🔁 Workflow

1. **Setup Azure VM** with SSH and Kafka ports open.
2. **Install Kafka, Python, Chrome, and dependencies** on the VM.
3. **Configure Blob Storage** with two containers.
4. **Run Producer** to scan for pirated streams every 5 minutes.
5. **Run Consumers** to save data in real time.
6. **Output Example**:
   - `links/2025-04-05T14:00:00.json`
   - `screenshots/screenshot_exiles.png`
   - `iframes/exiles.json`

---

## 🛠️ Step-by-Step Setup

### Step 1: Create Azure VM

- OS: Ubuntu 20.04 LTS  
- Size: B2S  
- Open ports: **22 (SSH)**, **9092 (Kafka)**

Create VM via Azure Portal:
- `+ Create a Resource` → `Virtual Machine`
- Set up Resource Group, name (`kafka-vm`), region (e.g., East US)
- Authentication: SSH Public Key
- After creation, note the public IP (e.g., `20.123.45.67`)

---

### Step 2: SSH into VM

```bash
ssh -i your-key.pem azureuser@<VM-IP>

```
## Step 3: Install Kafka

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openjdk-11-jdk -y
wget https://downloads.apache.org/kafka/3.6.1/kafka_2.13-3.6.1.tgz
tar -xzf kafka_2.13-3.6.1.tgz
cd kafka_2.13-3.6.1
```
**Start Kafka & Zookeeper**

```bash
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
bin/kafka-server-start.sh -daemon config/server.properties
```

**Create Kafka Topics**

```bash
bin/kafka-topics.sh --create --topic piracy-links --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
bin/kafka-topics.sh --create --topic piracy-evidence --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

## Step 4: Install Python + Chrome + Selenium

```bash
sudo apt install python3-pip chromium-browser -y
pip3 install kafka-python azure-storage-blob selenium
wget https://chromedriver.storage.googleapis.com/114.0.5735.90/chromedriver_linux64.zip
unzip chromedriver_linux64.zip
sudo mv chromedriver /usr/local/bin/
```

## Step 5: Set Up Azure Blob Storage

1. Go to **Azure Portal** → `+ Create a resource` → `Storage account`
2. Set the storage account name (e.g., `streamguardstorage`) and choose a region
3. Create the following **containers**:
   - `streamguard-links` (Private)
   - `streamguard-evidence` (Private)
4. Copy the **connection string** from the **Access Keys** tab

---

## Step 6: Producer Script (`KafkaProducer.ipynb`)

This script scrapes websites every 5 minutes and captures:

- Pirated link URL  
- Screenshot (`.png`)  
- iframe source metadata (`.json`)  

It then streams the collected JSON data to the appropriate Kafka topics.

---

## Step 7: Consumer Scripts

### `KafkaConsumer_Links.ipynb`

→ Listens to the `piracy-links` topic and uploads metadata to the `streamguard-links` container.

### `KafkaConsumer_Evidence.ipynb`

→ Listens to the `piracy-evidence` topic and uploads:

- Screenshots to `screenshots/`
- iframe sources to `iframes/`

---

## 🌐 Example Blob Storage Output

📁 streamguard-links/
  └── 2025-04-05T14:00:00.json

📁 streamguard-evidence/
  ├── screenshots/
  │   └── screenshot_exiles.png
  └── iframes/
      └── exiles.json

---

## 🧭 Way Forward

- 🔍 **ML Anomaly Detection** using Isolation Forest to detect piracy surges  
- 📊 **Dashboards** to visualize trends directly from Blob data  
- 📈 **Deep Analysis** using Alteryx to extract location/time-based insights  

---

![Jupyter Notebook](https://img.shields.io/badge/jupyter-%23FA0F00.svg?style=for-the-badge&logo=jupyter&logoColor=white)
![Selenium](https://img.shields.io/badge/-selenium-%43B02A?style=for-the-badge&logo=selenium&logoColor=white)
[![Microsoft Azure](https://custom-icon-badges.demolab.com/badge/Microsoft%20Azure-0089D6?logo=msazure&logoColor=white)](#)
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-000?style=for-the-badge&logo=apachekafka)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
[![JSON](https://img.shields.io/badge/JSON-000?logo=json&logoColor=fff)](#)












