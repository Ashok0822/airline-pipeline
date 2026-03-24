#  Airline On-Time Performance — Big Data Pipeline

**Course:** GIK2Q3 Applied Big Data and Cloud Computing  
**Student:** Medha Phatak, Pavani Nasika, Supraja Mandava, Ashok Enukonda, Harshil Patel
**Repository:** https://github.com/Ashok0822/airline-pipeline

---

##  Project Overview

A distributed data processing pipeline that ingests, processes
and analyses 11.4 GB of US domestic airline on-time performance
data from the Bureau of Transportation Statistics (BTS).

The pipeline uses:
- **Apache Spark** for large-scale distributed processing
- **Dask** for Python-native parallel processing + benchmark
- **Kubernetes (Minikube)** for container orchestration
- **AWS EC2 + S3** for cloud deployment

---

##  Dataset

| Property | Details |
|----------|---------|
| Source | Bureau of Transportation Statistics (BTS) |
| URL | https://transtats.bts.gov/PREZIP/ |
| Years | 2022, 2023, 2024, 2025 |
| Files | 47 CSV files |
| Raw size | 11.4 GB |
| Records | ~27 million flights |
| Processed | 0.39 GB (Parquet, 97% compression) |

---

##  Project Structure
```
airline_pipeline/
├── notebooks/
│   ├── 01_download_data.ipynb       # Download 47 CSV files from BTS
│   ├── 02_data_ingestion.ipynb      # CSV → Parquet (Spark)
│   ├── 03_spark_processing.ipynb    # Analytics with Apache Spark
│   ├── 04_dask_processing.ipynb     # Analytics with Dask + benchmark
│   └── 05_kubernetes_pipeline.ipynb # Kubernetes deployment
├── data/
│   ├── README.md                    # Download instructions + links
│   ├── raw/                         # CSV files (not included — see data/README.md)
│   └── processed/                   # Parquet files (not included)
├── docs/
├── src/
├── config/
├── tests/
├── .gitignore
├── README.md
└── requirements.txt
```

---

##  Setup Instructions

### 1. Prerequisites

| Software | Version |
|----------|---------|
| Python | 3.11+ (Anaconda) |
| Java | OpenJDK 17 (Temurin) |
| PySpark | 4.1.1 |
| Dask | 2025.1.0 |
| Minikube | v1.38.1 |
| Helm | v3.14.4 |
| AWS CLI | v1.44.61 |

### 2. Clone the repository
```bash
git clone https://github.com/Ashok0822/airline-pipeline.git
cd airline-pipeline
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Windows — Hadoop setup (required for Spark)

Download winutils.exe and hadoop.dll from:
https://github.com/cdarlint/winutils/tree/master/hadoop-3.2.0/bin

Place both files in `C:\hadoop\bin\`

### 5. Download data

Run `notebooks/01_download_data.ipynb`

This automatically downloads all 47 CSV files into `data/raw/`

See `data/README.md` for manual download instructions.

### 6. Run notebooks in order
```
01_download_data.ipynb       → Downloads 47 CSV files (11.4 GB)
02_data_ingestion.ipynb      → Converts CSV to Parquet (0.39 GB)
03_spark_processing.ipynb    → Spark analysis (6 questions)
04_dask_processing.ipynb     → Dask analysis + Spark vs Dask benchmark
05_kubernetes_pipeline.ipynb → Kubernetes deployment
```

>  Always select **Python (pyspark_env)** kernel in Jupyter Lab!

---

##  Key Findings

| Question | Finding |
|----------|---------|
| Worst airline | Frontier (F9) — 15.8 min avg delay |
| Best airline | Delta (DL) — 3.4 min avg delay |
| Worst month | July — 15.1 min avg delay |
| Best month | October — 1.9 min avg delay |
| #1 delay cause | Late Aircraft — 39.3% of all delay minutes |
| Weather delays | Only 5.8% (much less than expected!) |
| Worst route | RNO→JFK — 66.1 min avg delay |
| Best route | BTM→SLC — arrives 12.2 min early on average! |

---

##  Spark vs Dask Benchmark

| Operation | Spark | Dask | Winner |
|-----------|-------|------|--------|
| Airline groupBy | 0.87s | 3.2s |  Spark |
| Route analysis | 0.22s | 2.8s |  Spark |
| Delay root causes | 11.96s | 30.92s |  Spark |
| Monthly patterns | 6.21s | 8.4s |  Spark |

**Conclusion:** Spark is faster for large-scale ETL.
Dask is better for Python ML workflows and smaller datasets.

---

##  Kubernetes Deployment
```bash
# Start Minikube
minikube start --cpus=4 --memory=8192

# Deploy Dask with Helm
helm repo add dask https://helm.dask.org/
helm install my-dask dask/dask \
    --set scheduler.image.tag=2025.1.0 \
    --set worker.replicas=2 \
    --set jupyter.enabled=false

# Check pods
kubectl get pods

# Port forward
kubectl port-forward svc/my-dask-scheduler 8786:8786
kubectl port-forward svc/my-dask-scheduler 8787:80
```

---

##  AWS Cloud Deployment

| Component | Details |
|-----------|---------|
| Storage | S3 bucket: airline-pipeline-ashok (eu-north-1) |
| Compute | EC2 t3.micro — Amazon Linux 2023 |
| Security | IAM role: ec2-s3-read-role |
| Region | Europe Stockholm (eu-north-1) |
| Cost | $0.00 (free tier) |
```bash
# Upload data to S3
aws s3 cp data/processed/flights_clean.parquet \
    s3://airline-pipeline-ashok/data/processed/flights_clean.parquet \
    --recursive

# Download on EC2
aws s3 sync s3://airline-pipeline-ashok/data/processed/ \
    ~/airline_pipeline/data/processed/
```

---

##  Data

The `data/raw/` and `data/processed/` folders are excluded
from this repository due to file size (11.4 GB raw).

See `data/README.md` for download instructions.

---

