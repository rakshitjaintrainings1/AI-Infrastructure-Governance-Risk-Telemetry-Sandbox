# AI Infrastructure Governance & Risk Telemetry Sandbox

This repository contains the deployment manifests, network routing architectures, and live data telemetry loops for the AI Ingestion Gateway & Risk Telemetry Sandbox that I built. This sandbox was engineered to programmatically audit third-party foundational model API integrations (OpenAI, Gemini, Claude) under ISO/IEC 42001 and NIST AI RMF frameworks.

## Technical Architecture Overview
The lab I developed simulates a corporate Retrieval-Augmented Generation (RAG) pipeline parsing data out of internal storage layers (Snowflake/BigQuery). It tracks system health, maps boundary data lineage, intercepts clear-text PII leaks, charts data drift metrics (Population Stability Index), and executes an Infrastructure Circuit-Breaker Pattern to maintain systemic resilience during model degradation or vendor outages.

## My Prerequisites & Local Lab Environment Setup
To initialize this multi-container monitoring matrix locally, I configured my environment to satisfy the following technical requirements:

### 1. Hardware & System Virtualization Layer
*   Operating System: Windows 11 Professional (I verified that Hardware Virtualization was enabled inside the system motherboard UEFI/BIOS firmware).

### 2. Infrastructure & Hypervisor Engine
*   Docker Desktop: Installed, deployed, and kept actively running in the background.
*   Windows Subsystem for Linux (WSL2): Configured as the backend hypervisor engine to mount the containerized network bridges.

### 3. Application Runtime Environments
*   Python 3.10 via Anaconda Distribution: I utilized the Jupyter Notebook Workspace as my primary development environment because it is easier to use, execute, and navigate for running iterative telemetry scripts.

### 4. Required Python Risk Libraries
I installed the specific system dependencies natively inside my Python runtime environment using a process execution block to programmatically manage file locks:
*   `prometheus_client`
*   `openlineage-python`
*   `pydantic`

---

## The Tech Stack Configuration Blueprints I Created

### 1. My Container Array Deployment (docker-compose.yml)
I mapped the services to the 3020 and 9092 alternative port ranges to completely insulate the local environment from network conflicts with internal Anaconda processes.
```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9092:9090"
    volumes:
      - ./prometheus_v2.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3020:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_PATHS_DATA=/tmp/grafana-data
```

### 2. My Telemetry Ingestion Architecture (prometheus_v2.yml)
I implemented the host bridge targeting configuration file below to allow the isolated Prometheus container to safely scrape telemetry metrics from my physical Windows host machine.
```yaml
global:
  scrape_interval: 2s

scrape_configs:
  - job_name: 'ai_infrastructure_telemetry'
    metrics_path: '/'
    static_configs:
      - targets: ['host.docker.internal:8000']
```

### 3. My Ingestion Control & Simulation Pipeline (ai_governance_pipeline.py)
I authored this comprehensive telemetry script to simulate data streams, catch PII data leaks, evaluate model drift, and trigger automated circuit breakers.
```python
import time
import random
from prometheus_client import start_http_server, Counter, Gauge

PII_LEAK_COUNTER = Counter('api_pipeline_pii_leak_total', 'Total clear-text PII attributes intercepted')
MODEL_DRIFT_GAUGE = Gauge('api_pipeline_model_drift_psi', 'Current Population Stability Index drift metric')
CIRCUIT_BREAKER_STATUS = Gauge('api_gateway_circuit_breaker_active', '1 = Fallback Active, 0 = Normal')

def execute_api_audit_cycle(cycle_id):
    print(f"\n[PIPELINE AUDIT] Processing RAG Ingestion Cycle #{cycle_id}...")
    
    # I built this boundary logic to deliberately inject and catch clear-text PII every 3rd cycle
    if cycle_id % 3 == 0:
        PII_LEAK_COUNTER.inc()
        print("WORKSPACE FAILURE: Clear-text PII detected in outbound API payload packet!")
    else:
        print("Lineage Check: Outbound payload successfully scrubbed and anonymized.")

    # I simulated a data shift here to create a dynamic line graph visualization
    current_drift = round(random.uniform(0.04, 0.12), 2) if cycle_id < 6 else round(random.uniform(0.23, 0.45), 2)
    MODEL_DRIFT_GAUGE.set(current_drift)
    print(f"Telemetry Monitor: Current Model Drift (PSI Value) = {current_drift}")
    
    # Enforcing the automated infrastructure threshold rule
    if current_drift > 0.20:
        print("THRESHOLD BREACH (PSI > 0.20): Activating Automated Circuit Breaker!")
        CIRCUIT_BREAKER_STATUS.set(1)
    else:
        CIRCUIT_BREAKER_STATUS.set(0)

if __name__ == '__main__':
    start_http_server(8000)
    print("TECHNICAL AI AUDIT SANDBOX STATION ACTIVE on http://localhost:8000")
    cycle = 1
    while True:
        execute_api_audit_cycle(cycle)
        cycle += 1
        time.sleep(3)
```

---

## My Step-by-Step Execution Playbook

1.  **Booting the Dashboards:** I opened my terminal inside the working folder and executed the background containerization layer:
    ```bash
    docker compose up -d
    ```
2.  **Activating the Telemetry Data Pipeline:** I executed the core Python script directly inside my Jupyter Notebook workspace to begin streaming live system risk parameters onto port 8000.
3.  **Configuring the Visualizations:** I opened my browser and navigated to the Grafana interface at `http://localhost:3020` (using the admin credentials I configured).
    *   I went to data sources and added **Prometheus**. I resolved container network isolation by configuring the connection URL to target the internal bridge endpoint: `http://docker.internal`.
    *   I created a fresh dashboard panel, toggled the interface from Builder mode directly to **Code Mode**, and input my precise metric parameters: `api_pipeline_model_drift_psi` and `api_pipeline_pii_leak_total`.
    *   Finally, I shifted the baseline timeframe dropdown window filter from *Last 6 hours* down to **Last 5 minutes**. This successfully forced the Grafana panel to render my live, active Python data stream into high-resolution, multi-colored operational line charts.
  
[  1. Ingestion Phase ]
  Customer asks a question -> Chatbot pulls raw transaction data from Snowflake/BigQuery.
       │
       ▼
[ 2. Data Lineage & PII Control Node ]
  System scans the outbound text payload block for clear-text PII (e.g., "@" symbol).
       ├──► [ Breach Found (Cycle % 3 == 0) ] ──► Increments "api_pipeline_pii_leak_total" in Prometheus database.
       └──► [ Clean Data ]
       │
       ▼
[ 3. Model Telemetry & Drift Monitoring ]
  Pipeline continuously calculates the Population Stability Index (PSI) to measure data decay.
       │
       ▼
[ 4. Automated Infrastructure Constraint (The Circuit Breaker) ]
  Is the calculated model drift metric higher than the acceptable threshold (PSI > 0.20)?
       ├──► [ YES ] ──► Sets status to "1" ──► Drops external LLM API route -> Services local safe fallback.
       └──► [ NO ]  ──► Sets status to "0" ──► Routes data normally via external API (OpenAI/Gemini/Claude).
       │
       ▼
[ 5. Visual Control Tower Tower ]
  Grafana pulls numeric timelines from Prometheus and displays live updates on a 5-minute window chart.

