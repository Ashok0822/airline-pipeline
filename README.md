 # Airline On-Time Performance — Big Data Pipeline

**Course:** GIK2Q3 Applied Big Data and Cloud Computing  
**Student:** GROUPA2(Ashok Enukonda, Pavani Mandava, Medha Phatak, Supraja Mandava, Harshil Patel)

---

## Project Overview

A distributed data processing pipeline that ingests, processes, and analyses 11.4 GB of US domestic airline on-time performance data from the Bureau of Transportation Statistics (BTS). The pipeline uses Apache Spark and Dask for distributed processing, deploys to Kubernetes (Minikube), and runs on AWS cloud (EC2 + S3).

---

## Dataset

| Property | Details |
|----------|---------|
| Source | Bureau of Transportation Statistics (BTS) |
| Years | 2022, 2023, 2024, 2025 |
| Files | 47 CSV files |
| Raw size | 11.4 GB |
| Records | ~27 million flights |
| Processed | 0.39 GB (Parquet, 97% compression) |

---

## Project Structure
```
airline_pipeline/
├── notebooks/
│   ├── 01_download_data.ipynb      # Download 47 CSV files from BTS
│   ├── 02_data_ingestion.ipynb     # CSV → Parquet (Spark)
│   ├── 03_spark_processing.ipynb   # Analytics with Apache Spark
│   ├── 04_dask_processing.ipynb    # Analytics with Dask + benchmark
│   └── 05_kubernetes_pipeline.ipynb # Kubernetes deployment
├── data/
│   ├── README.md                   # Download instructions
│   └── raw/                        # CSV files (not included — see below)
├── docs/
├── src/
├── config/
├── tests/
├── .gitignore
├── README.md
└── requirements.txt
```

---

## Setup Instructions

### 1. Prerequisites

- Python 3.11+ (Anaconda recommended)
- Java 17 (OpenJDK Temurin)
- Apache Spark 4.1.1
- Minikube + Helm (for Kubernetes deployment)
- AWS CLI (for cloud deployment)

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Download Data

Run the download notebook or follow instructions in `data/README.md`:
```bash
jupyter lab
# Then run: notebooks/01_download_data.ipynb
```

### 4. Run Pipeline

Run notebooks in order:
```
01_download_data.ipynb      → Downloads 47 CSV files (11.4 GB)
02_data_ingestion.ipynb     → Converts to Parquet (0.39 GB)
03_spark_processing.ipynb   → Spark analysis (6 questions)
04_dask_processing.ipynb    → Dask analysis + benchmark
05_kubernetes_pipeline.ipynb → Kubernetes deployment
```

### 5. Kernel

Always select **Python (pyspark_env)** kernel in Jupyter Lab.

---

## Environment Variables

Set these before starting Spark (already in notebooks):
```python
os.environ["JAVA_HOME"]   = r"C:\Program Files\Eclipse Adoptium\jdk-17.0.18.8-hotspot"
os.environ["HADOOP_HOME"] = r"D:\hadoop"
```

---

## Key Findings

| Question | Finding |
|----------|---------|
| Worst airline | Frontier (F9) — 15.8 min avg delay |
| Best airline | Delta (DL) — 3.4 min avg delay |
| Worst month | July — 15.1 min avg delay |
| Best month | October — 1.9 min avg delay |
| #1 delay cause | Late Aircraft — 39.3% of all delay minutes |
| Weather delays | Only 5.8% (much less than people think!) |
| Worst route | RNO→JFK — 66.1 min avg delay |
| Best route | BTM→SLC — arrives 12.2 min early! |

---

## Spark vs Dask Benchmark

| Operation | Spark | Dask | Winner |
|-----------|-------|------|--------|
| Airline groupBy | 0.87s | 3.2s | Spark |
| Route analysis | 0.22s | 2.8s | Spark |
| Delay root causes | 11.96s | 30.92s | Spark |
| Monthly patterns | 6.21s | 8.4s | Spark |

---

## Kubernetes Deployment
```bash
minikube start --cpus=4 --memory=8192
helm repo add dask https://helm.dask.org/
helm install my-dask dask/dask \
    --set scheduler.image.tag=2025.1.0 \
    --set worker.replicas=2 \
    --set jupyter.enabled=false
kubectl get pods
```

---

## AWS Cloud Deployment

| Component | Details |
|-----------|---------|
| Storage | S3 bucket: airline-pipeline-ashok (eu-north-1) |
| Compute | EC2 t3.micro — Amazon Linux 2023 |
| Security | IAM role: ec2-s3-read-role |
| Cost | $0.00 (free tier) |

---

## Data Download

The `data/raw/` and `data/processed/` folders are excluded from this repository due to file size (11.4 GB raw).

**To download the data:**
1. Run `notebooks/01_download_data.ipynb`
2. Or follow instructions in `data/README.md`

