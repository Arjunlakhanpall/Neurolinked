# Mind-to-Script — Brain-to-Text Decoding System

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch 2.2](https://img.shields.io/badge/PyTorch-2.2.0-ee4c2c.svg)](https://pytorch.org/)
[![AWS Optimized](https://img.shields.io/badge/AWS-S3%20%7C%20EC2%20%7C%20CloudWatch-orange.svg)](https://aws.amazon.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Mind-to-Script** is a high-performance, non-invasive Brain-Computer Interface (BCI) designed to decode multi-channel EEG signals into English text. By leveraging a cross-modal "Bridge" architecture (1D-CNN + BiLSTM) and a pre-trained BART decoder, the system translates neural oscillations into natural language in real-time.

---

## 🏗 System Architecture

The system is built for scalability and production-readiness, following a modular MLOps lifecycle:

1.  **Ingestion:** Raw ZuCo 2.0 (`.mat`/`.edf`) data is ingested and canonicalized.
2.  **The Bridge:** A custom encoder maps temporal EEG features into the latent space of a Transformer.
3.  **Inference:** A FastAPI service hosted on **AWS EC2 (g4dn)** processes signals with built-in **Signal Quality Index (SQI)** validation.
4.  **Storage:** All artifacts (models, shards, logs) are versioned on **Amazon S3**.

```text
EEG Device -> FastAPI (API Gateway) -> Preprocessing (MNE) -> Bridge Model -> BART Decoder -> Text Output

```

---

## 🛠 Tech Stack

| Component | Technology |
| --- | --- |
| **Deep Learning** | PyTorch, HuggingFace (BART), MNE-Python |
| **Backend** | FastAPI, Uvicorn, SQLAlchemy |
| **Cloud (AWS)** | S3, EC2 (NVIDIA T4), IAM, CloudWatch |
| **Ops & Monitoring** | Docker, Prometheus, GitHub Actions |
| **Database** | PostgreSQL (Production) / SQLite (Dev) |

---

## 📁 Project Structure

```bash
.
├── app/                  # Production API
│   ├── main.py           # FastAPI entrypoint & SQI logic
│   ├── model.py          # Bridge architecture (CNN+BiLSTM)
│   └── db.py             # Inference result persistence
├── scripts/              # Data & Training Pipeline
│   ├── load_zuco.py      # Raw data ingestion
│   ├── align_zuco.py     # Word-level onset/offset alignment
│   └── train_shards.py   # Shard-based training on GPU
├── docker/               # Containerization
│   ├── Dockerfile        # CUDA 11.8 base image
│   └── docker-compose.yml# Multi-service local orchestration
├── requirements.txt      # Dependency manifest
└── DESIGN.md             # Core design & AWS specifications

```

---

## 📅 30-Day Implementation Roadmap

### **Week 1: Data Engineering**

* Setup AWS S3 bucket versioning & IAM policies.
* Implement MNE-based preprocessing (Band-pass 0.5–50 Hz, ICA).
* Generate versioned data shards (`v1.0.0`) for reproducible training.

### **Week 2: Model & Bridge Training**

* Implement the EEG-to-BART projection layer.
* Fine-tune the "Bridge" on ZuCo 2.0 shards.
* Achieve baseline Word Error Rate (WER) and BLEU scores.

### **Week 3: Deployment & Monitoring**

* Build Docker images with NVIDIA Container Toolkit support.
* Deploy to EC2 `g4dn.xlarge` using AWS Deep Learning AMI.
* Configure Prometheus metrics for latency and SQI tracking.

### **Week 4: CI/CD & Hardening**

* Automate model reloads via S3 versioned prefixes.
* Implement GitHub Actions for unit tests and linting.
* Set up CloudWatch billing and GPU-utilization alarms.

---

## ⚡ Quickstart

### 1. Installation

```bash
git clone [https://github.com/your-username/mind-to-script.git](https://github.com/your-username/mind-to-script.git)
cd mind-to-script
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt

```

### 2. Local Development (API)

```bash
# Start the API with hot-reload
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

```

### 3. Build Docker Image

```bash
docker compose build
docker compose up

```

---

## 🔒 Security & Governance

* **IAM Roles:** EC2 instances use narrow-scope IAM roles for S3/CloudWatch access (No static keys).
* **Data Validation:** Hard rejection (HTTP 422) for signals with SQI < 0.25.
* **Infrastructure as Code:** Recommended deployment via Terraform (see `Appendix` in DESIGN.md).

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.

---

**Maintained by:** Arjun Lakhanpal

**Last Updated:** 2026-02-11

