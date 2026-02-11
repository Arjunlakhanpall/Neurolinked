# Neurolinked: Non-Invasive Brain-to-Text Decoding

**Neurolinked** (also known as **Mind-to-Script**) is an end-to-end research and deployment framework designed to translate multi-channel EEG signals into natural language text. By bridging neural oscillations with a pre-trained BART Transformer, the system provides a robust pipeline for real-time brain-computer interface (BCI) communication.

---

## ℹ️ Project Overview

Translating raw brain activity into coherent text remains a "holy grail" of BCI research. Neurolinked addresses this by using a cross-modal "Bridge" architecture. It captures temporal and spatial brain patterns via a 1D-CNN + BiLSTM encoder and projects them into the latent space of a language model. This approach preserves the linguistic power of the decoder while allowing the encoder to specialize in noisy, high-dimensional neural data.

### Key Features

* **Automated MNE Preprocessing:** Reproducible ICA, band-pass filtering (0.5–50 Hz), and artifact rejection.
* **Neural Bridge Module:** Maps EEG features directly to the `d_model` embedding space of BART.
* **Production-Ready API:** FastAPI service with integrated Signal Quality Index (SQI) monitoring and Prometheus metrics.
* **AWS Integration:** Native support for S3 model versioning and EC2 GPU inference.

---

## 🛠️ System Architecture

The Neurolinked pipeline is divided into three distinct phases:

1. **Data Engineering:** Raw signals from the **ZuCo 2.0** dataset are aligned with word-level gaze duration and sharded into `.pt` files for high-speed training.
2. **Latent Alignment:** The "Bridge" model is trained to minimize the distance between EEG-derived embeddings and the target text's semantic embeddings.
3. **Real-Time Inference:** Incoming signals are validated via an SQI check. If the signal passes quality thresholds (), it is decoded by the BART Transformer.

---

## 🚀 Getting Started

### Prerequisites

* **Python 3.10+**
* **NVIDIA Docker Toolkit** (for GPU acceleration)
* **AWS CLI** (optional, for cloud artifact management)

### Installation

```bash
git clone https://github.com/your-username/neurolinked.git
cd neurolinked
pip install -r requirements.txt

```

### Quick Usage

To run a local inference test with synthetic data:

```bash
python scripts/run_demo_with_bart.py

```

To launch the FastAPI production server:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000

```

---

## 📈 Research & Evaluation

We evaluate the system using standard NLP metrics alongside BCI-specific benchmarks:

* **Word Error Rate (WER):** Primary metric for transcription accuracy.
* **BLEU-4 / ROUGE-L:** Evaluation of linguistic fluency and semantic overlap.
* **Signal Quality Gauges:** Gauge metrics for real-time hardware performance monitoring.

### Citation

If you use this project in your research, please cite the underlying ZuCo dataset:

```bibtex
@article{hollenstein2020zuco,
  title={ZuCo 2.0: A Dataset of Physiological Signals during Natural Reading and Annotation},
  author={Hollenstein, Nora and et al.},
  journal={Scientific Data},
  year={2020}
}

```

---

## 🔒 Security & Safety

* **IAM Roles:** The system is designed to use IAM instance profiles for AWS access—never hardcode your keys.
* **Privacy:** All inference results are stored locally or in your private DB; no data is sent to external AI providers unless explicitly configured.

