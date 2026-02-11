# Neurolinked: Non-Invasive Brain-to-Text Decoding via Latent Alignment

**Neurolinked** (also known as **Mind-to-Script**) is an end-to-end research and deployment framework designed to decode natural language from non-invasive electroencephalogram (EEG) signals. By bridging neural oscillations with the latent space of a pre-trained Transformer (BART), the system provides a pathway for real-time assistive communication and neural linguistics research.

---

## 📖 1. Research & Theoretical Foundation

Neurolinked is grounded in the **Latent Alignment** hypothesis, which suggests that neural representations of language during reading or imagined speech contain semantic markers that can be mapped onto the embedding space of Large Language Models (LLMs).

### 1.1 State-of-the-Art Context (2024–2026)

Current research has shifted from simple motor imagery classification to **Generative BCI (BCI 2.0)**:

* **The R1 Translator (2025):** Demonstrated that bidirectional LSTMs outperform traditional adapters in capturing the sequential dependencies of brainwaves.
* **CET-MAE (2024):** Introduced contrastive masked autoencoders to bridge the semantic gap between EEG and text.
* **Evaluation Rigor:** Our system addresses "Teacher Forcing" fallacies by including **Noise Baselines** to ensure the model isn't simply "hallucinating" coherent text from random noise.

### 1.2 Core Bibliography

1. **Hollenstein, N., et al. (2020/2024).** *ZuCo 2.0: A Dataset of Physiological Signals during Natural Reading.* Scientific Data.
2. **Wang, J., & Ji, S. (2022/2025).** *Open Vocabulary EEG-to-Text Decoding.* 3. **Duan, Y., et al. (2024).** *DeWave: Discrete EEG-to-Text Generation.*
3. **Masry, A. (2025).** *Multimodal Feature Encoding in BCI.*

---

## 🏗️ 2. System Architecture

The Neurolinked architecture follows a modular **Signal → Latent → Text** pipeline.

### 2.1 The Preprocessing Engine (MNE-based)

Raw EEG signals have an extremely low Signal-to-Noise Ratio (). Our pipeline implements:

* **Band-pass Filtering:** 0.5–50 Hz to capture Delta through Gamma bands.
* **Artifact Rejection:** Independent Component Analysis (ICA) to isolate EOG (eye) and EMG (muscle) noise.
* **Normalization:** Subject-specific Z-scoring to account for individual cortical variations.

### 2.2 The "Bridge" Encoder

The Bridge is a cross-modal adapter consisting of:

* **1D-CNN Layer:** For local temporal feature extraction across electrodes.
* **BiLSTM Layer:** To capture long-range dependencies in the "flow" of thought.
* **Linear Projection:** A learnable layer that aligns the BiLSTM output with the 768-dimension embedding space of **BART-base**.


<img width="2013" height="348" alt="eeg2text-open-vocabulary-eeg-to-text-decoding-with-eeg-pre-training-and-multi-view-transformer-1" src="https://github.com/user-attachments/assets/57bd8057-d31b-4f9c-9a07-9930fff81de9" />

---

## 🚀 3. Installation & Usage

### 3.1 Environment Setup

```bash
git clone https://github.com/Arjunlakhanpall/Neurolinked.git
cd Neurolinked
python -m venv .venv
source .venv/bin/activate  # Windows: .\.venv\Scripts\Activate.ps1
pip install -r requirements.txt

```

### 3.2 Data Preparation

```bash
# Canonicalize raw ZuCo .mat files
python scripts/load_zuco.py

# Create sharded training data
python scripts/build_manifest_and_shards.py --canonical ./data/canonical --out ./data/shards --version v1.0.0

```

### 3.3 Training & Inference

```bash
# Train the Bridge model
python scripts/train_from_shards.py --version v1.0.0 --epochs 50

# Launch Production API
uvicorn app.main:app --host 0.0.0.0 --port 8000

```

---

## 📊 4. Evaluation & Scientific Rigor

Unlike standard NLP, BCI decoding requires multi-dimensional validation:

| Metric | Target | Purpose |
| --- | --- | --- |
| **WER** | < 40% | Measures raw transcription accuracy. |
| **BLEU-4** | > 0.15 | Measures linguistic fluency. |
| **SQI** | > 0.25 | Hardware performance monitoring. |
| **Noise Baseline** | Δ > 10% | Confirms performance vs. random Gaussian noise. |

---
<img width="1787" height="968" alt="eeg-to-text-translation-a-model-for-deciphering-human-brain-activity-2" src="https://github.com/user-attachments/assets/63cdf6b9-4e6f-4adc-b2dd-c0ca96132c4b" />
---
## ☁️ 5. Deployment (AWS MLOps)

* **Storage:** All models and datasets are versioned on **S3** under explicit prefixes.
* **Compute:** Deployment is optimized for **EC2 g4dn.xlarge** instances using the NVIDIA T4 GPU.
* **Security:** Use **IAM Instance Profiles** for S3 access. **Never** hardcode AWS Access Keys in the `.env` file.
* **Monitoring:** Prometheus tracks `/metrics`, including `eeg_inference_latency` and `gpu_memory_utilization`.

---

## 📜 6. Citation

If you use this project in your research, please cite the underlying ZuCo dataset and this repository:

```bibtex
@article{hollenstein2020zuco,
  title={ZuCo 2.0: A Dataset of Physiological Signals during Natural Reading and Annotation},
  author={Hollenstein, Nora and et al.},
  journal={Scientific Data},
  year={2020}
}

```

---

## 🔒 7. Ethics & Safety

* **Privacy:** EEG data is highly sensitive "biometric" data. Neurolinked is designed to run locally or on private cloud instances.
* **Medical Disclaimer:** This is a **research tool**, not a medical device. It is not intended for clinical diagnosis.

---

**Maintained by:** Arjun Lakhanpal
**Version:** 2026.02.11-Alpha


