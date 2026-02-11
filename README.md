# Neurolinked: Non-Invasive Brain-to-Text Decoding

**Neurolinked** (a.k.a. **Mind-to-Script**) is an end-to-end research and deployment toolkit that converts multi-channel EEG signals into natural-language text. By bridging neural oscillations with a pre-trained BART transformer, the system enables high-fidelity brain-to-text translation for assistive technology and cognitive research.

---

## ✨ Features

* **Reproducible Pipeline:** Automated MNE-based preprocessing (ICA, artifact removal, band-pass filtering).
* **Neural Bridge Architecture:** A 1D-CNN + BiLSTM encoder that projects EEG features into the `d_model` embedding space of a Transformer.
* **Cloud-Native Inference:** GPU-optimized FastAPI service ready for AWS deployment (g4dn/p3).
* **Signal Integrity:** Real-time Signal Quality Index (SQI) monitoring to ensure decoding accuracy.
* **MLOps Ready:** Versioned data/model artifacts on S3, Prometheus monitoring, and persistence via SQLAlchemy.

---

## 🛠️ Tech Stack

| Category | Technology |
| --- | --- |
| **Deep Learning** | PyTorch, HuggingFace (BART), MNE-Python |
| **Backend & API** | FastAPI, Uvicorn, Docker, NVIDIA-Container-Toolkit |
| **Cloud (AWS)** | S3 (Versioning), EC2 (G4dn), IAM Roles, CloudWatch |
| **Observability** | Prometheus, SQLAlchemy, Structured JSON Logging |

---

## 🚀 Quick Start

### 1. Prerequisites

* Python 3.10+
* Docker & NVIDIA Container Toolkit (for GPU inference)
* AWS CLI configured (for artifact fetching)

### 2. Installation

```bash
git clone https://github.com/your-repo/neurolinked.git
cd neurolinked
python -m venv .venv
source .venv/bin/activate  # Windows: .\.venv\Scripts\Activate.ps1
pip install -r requirements.txt

```

### 3. Run Inference API (Development)

```bash
export MODEL_DIR="./models"
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

```

---

## 🧬 Data & Training Workflow

The system is optimized for the **ZuCo 2.0** dataset. Follow this cadence to train a custom bridge:

1. **Ingestion:** Convert raw `.mat`/`.edf` files to canonical pickles.
```bash
python scripts/load_zuco.py

```


2. **Sharding:** Build per-word shards for efficient PyTorch loading.
```bash
python scripts/build_manifest_and_shards.py --canonical data/canonical --out data/shards --version v1.0.0

```


3. **Training:** Train the CNN+BiLSTM bridge.
```bash
python scripts/train_from_shards.py --version v1.0.0 --shard-root ./data/shards --model-dir ./models

```



---

## ☁️ Deployment & Ops

* **S3 Artifacts:** Store models under versioned prefixes: `s3://bucket/mindtoscript/models/v1.0.0/`.
* **Infrastructure:** Recommended EC2 instance `g4dn.xlarge` using the Deep Learning AMI.
* **Security:** Use **IAM Instance Roles** to grant S3 read access to the EC2 host.
* **Monitoring:** Access Prometheus metrics at `/metrics` to track latency, GPU memory, and rejection rates.

---

## 📄 Documentation & Citations

For detailed architecture diagrams and the 30-day implementation roadmap, refer to [DESIGN.md](https://www.google.com/search?q=./DESIGN.md).

### Citation

If you use this code for research, please cite the underlying ZuCo dataset:

```bibtex
@article{hollenstein2020zuco,
  title={ZuCo 2.0: A Dataset of Physiological Signals during Natural Reading and Annotation},
  author={Hollenstein, Nora and et al.},
  journal={arXiv preprint arXiv:1912.00036},
  year={2020}
}

```

---

**Next Step:** I can generate a **one-click Terraform template** to provision your AWS S3 and EC2 infrastructure. Would you like me to add that to the repo?
