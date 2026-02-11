# Mind-to-Script: End-to-End EEG-to-Text Decoding

**Mind-to-Script** is a non-invasive Brain-Computer Interface (BCI) framework designed to translate multi-channel EEG signals into natural language text. By bridging neural oscillations with the latent space of a Transformer, the system provides a pathway for real-time assistive communication.

---

## 📖 1. Research & Theoretical Foundation

This project is built upon state-of-the-art research in neural decoding and cross-modal alignment.

### Core Literature

| Paper | Contribution to Project |
| --- | --- |
| **R1 Translator (2025)** | The inspiration for our **BiLSTM + BART** architecture, achieving SOTA on the ZuCo dataset. |
| **ZuCo 2.0 (Hollenstein et al.)** | Our primary corpus, providing simultaneous EEG and eye-tracking data for word-level alignment. |
| **EEG-to-Text via MLSTM (2024)** | Validated the use of mapping modules to bridge high-dimensional EEG features with text decoders. |
| **Graph-Enhanced Decoding (2025)** | Informed our spatial electrode mapping logic (10-20 system). |

### The "Bridge" Hypothesis

We hypothesize that neural signals during reading contain temporal and spectral features that map non-linearly to the semantic embeddings of a Language Model (LM). Our "Bridge" model acts as an adapter, translating **micro-volt fluctuations** into **BART hidden states**.

---

## 🗺️ 2. System Architecture & Flow

The system follows a strict pipeline from the scalp to the screen.

### High-Level Flowchart

### Technical Component Breakdown

1. **Signal Acquisition:** Multi-channel EEG (e.g., 62-channel 10-20 montage).
2. **Preprocessing (MNE):** * **Filtering:** 0.5–50 Hz Band-pass.
* **Denoising:** ICA-based EOG/EMG artifact rejection.
* **Alignment:** Word-level epoching based on gaze duration.


3. **The Bridge (Encoder):** 1D-CNN + BiLSTM.
4. **The Decoder:** Frozen `facebook/bart-base` for robust natural language generation.

---

## 🛠️ 3. Implementation Roadmap (30 Days)

| Phase | Focus | Deliverables |
| --- | --- | --- |
| **Days 1-7** | **Data Engineering** | MNE pipeline, S3 bucket setup, ZuCo 2.0 canonical shards. |
| **Days 8-15** | **Model Dev** | Bridge architecture implementation, latent alignment training. |
| **Days 16-23** | **Deployment** | FastAPI container, AWS g4dn (T4 GPU) setup, Prometheus metrics. |
| **Days 24-30** | **Hardening** | CI/CD via GH Actions, SQI-based guardrails, WER/BLEU evaluation. |

---

## 🔌 4. Deployment & MLOps

### Configuration (.env)

```env
# AWS Settings
AWS_REGION=us-east-1
MODEL_S3_URI=s3://mindtoscript/models/v1.0.0/

# Inference Constraints
SQI_THRESHOLD=0.25      # Hard reject below this quality
AMPLITUDE_LIMIT=100.0   # Reject if >100µV (Artifacts)

# Performance
USE_CUDA=True
BATCH_SIZE=32

```

### Operational Guardrails

To ensure scientific validity, the system implements:

* **Noise Baselines:** Periodic testing against Gaussian noise to ensure the model isn't "hallucinating" text without real EEG input.
* **Signal Quality Index (SQI):** Real-time monitoring of channel impedance and signal-to-noise ratio.
* **Monitoring:** CloudWatch alerts for GPU memory leaks and high WER (Word Error Rate) spikes.

---

## 📁 5. Directory Structure

```bash
.
├── app/                # FastAPI Inference Service
│   ├── main.py         # API entrypoint & SQI logic
│   ├── model.py        # Bridge (CNN+BiLSTM) implementation
│   └── db.py           # Inference result persistence (PostgreSQL)
├── scripts/            # Research & Data Pipeline
│   ├── load_zuco.py    # Raw data processing
│   ├── train_shards.py # GPU training script
│   └── evaluate.py     # WER/BLEU/ROUGE scoring
├── docker/             # Containerization
│   └── Dockerfile      # PyTorch + CUDA 11.8 base
└── .github/            # CI/CD Workflows

```

---

## ⚡ 6. Execution Commands

### Local Training

```bash
python scripts/train_shards.py --version v1.0.0 --epochs 50 --batch 64

```

### Build Production Container

```bash
docker build -t mindtoscript:v1 -f docker/Dockerfile .
docker run --gpus all -p 8000:8000 mindtoscript:v1
