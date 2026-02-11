# Neurolinked — Mind-to-Script (EEG → Text)

Neurolinked (a.k.a. Mind-to-Script) is an end-to-end research and deployment toolkit that converts non-invasive EEG recordings into natural-language text. It provides data preprocessing, dataset sharding, a CNN+BiLSTM "bridge" that projects EEG features into transformer embedding space, and a FastAPI service for inference with monitoring and persistence.

Why this repo
--------------
This codebase bundles reproducible pipelines for:
- EEG preprocessing and artifact removal (MNE-based)
- Per-word epoch extraction and dataset sharding for supervised training
- A trainable EEG encoder + projection ("bridge") that interfaces with a pretrained BART decoder
- A GPU-ready Dockerized inference service (FastAPI) with SQI checks, Prometheus metrics, and DB persistence

Quick overview (how to get started)
-----------------------------------
1. Create and activate a Python virtual environment:

   python -m venv .venv
   # PowerShell
   .\.venv\Scripts\Activate.ps1
   # or Unix
   source .venv/bin/activate

2. Install minimal test dependencies (fast):

   pip install -r requirements-ci.txt
   pip install --index-url https://download.pytorch.org/whl/cpu torch==2.2.0+cpu

3. Run unit tests:

   pytest -q

4. Start the API server for local testing:

   $env:MODEL_DIR = "./models"         # PowerShell
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

Core endpoints
--------------
- POST /infer — Accepts JSON `{ "signals": [[...], ...], "sfreq": 500 }` and returns {text, confidence, meta}.
- GET /metrics — Prometheus-formatted metrics (latency, SQI, GPU memory, rejections).
- GET /history — Recent inferences persisted to the DB.

Data & training workflow
------------------------
1. Convert raw ZuCo files to canonical pickles:
   scripts/load_zuco.py
2. Build per-word shards for training:
   scripts/build_manifest_and_shards.py --canonical data/canonical --out data/shards --version v1.0.0
3. Train the bridge:
   python scripts/train_from_shards.py --version v1.0.0 --shard-root ./data/shards --model-dir ./models

Deployment & ops
-----------------
- Store datasets and model artifacts on S3 with explicit version prefixes, e.g., `s3://bucket/mindtoscript/models/v1.0.0/`.
- Use IAM instance roles (no plaintext credentials) and CloudWatch for billing/health alerts.
- Docker compose is provided for local and EC2 deployment; the Dockerfile is GPU-ready.

Safety & observability
----------------------
- Signal Quality Index (SQI) guards against low-quality EEG (hard reject if SQI < 0.25).
- Prometheus metrics include inference latency, SQI, GPU memory usage, and hard-reject counter.
- Inference results are persisted to a relational DB for auditing and drift monitoring.

Files to review
---------------
- DESIGN.md — architecture, timeline and cloud runbook
- scripts/ — data processing, shard building, training and demo scripts
- notebooks/ — end-to-end synthetic demo
- app/ — FastAPI service, model loader, DB integration

Contributing, license & citations
---------------------------------
Please add a LICENSE file and include proper citations for ZuCo and related papers when using this dataset. For contributions, follow the CI checks in `.github/workflows/ci.yml`.

Questions or next steps
-----------------------
To run a full demo on your machine or cloud, follow DESIGN.md. If you want I can add a one-click Terraform template for S3 + IAM + EC2 or generate a PNG architecture diagram — tell me which and I’ll add it.
