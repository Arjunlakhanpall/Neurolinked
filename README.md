# Neurolinked (Mind-to-Script)

Lightweight EEG → Text prototype and toolkit (preprocessing, bridge training, FastAPI serving, and deployment helpers).

CI status: ![CI](https://github.com/Arjunlakhanpall/Neurolinked)

See `DESIGN.md` for architecture, timeline, and full runbook.

Quickstart (local dev)
----------------------
1. Create & activate venv (Windows PowerShell):

    python -m venv .venv
    .\.venv\Scripts\Activate.ps1

2. Install test/runtime deps (fast path for tests):

    pip install -r requirements-ci.txt
    pip install --index-url https://download.pytorch.org/whl/cpu torch==2.2.0+cpu

3. Run unit tests:

    pytest -q

4. Start the FastAPI server (dev):

    $env:MODEL_DIR = ".\models"
    uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

Endpoints
---------
- POST /infer — JSON body: `{ "signals": [[...], ...], "sfreq": 500 }`
- GET  /metrics — Prometheus metrics
- GET  /history — recent inference results from the DB

Example curl (quick smoke):

    curl -X POST http://127.0.0.1:8000/infer \
      -H "Content-Type: application/json" \
      -d '{"signals":[[0,0,0,0],[0,0,0,0]],"sfreq":500}'

Docker (build & run)
--------------------
Build (local Docker Desktop):

    docker compose build

Run API (no GPU required for dev):

    docker compose up -d web

Run the one-shot example (container runs scripts/run_example_inference.py):

    MODEL_S3_URI=s3://bucket/path/ docker compose run --rm example

Training & data pipeline (references)
-------------------------------------
- Create canonical pickles from ZuCo files:
  scripts/load_zuco.py
- Build shards (per-word epochs):
  scripts/build_manifest_and_shards.py --canonical data/canonical --out data/shards --version v1.0.0
- Train bridge from shards:
  python scripts/train_from_shards.py --version v1.0.0 --shard-root ./data/shards --model-dir ./models

Notes & best practices
----------------------
- Put models and dataset artifacts on S3 under versioned prefixes (e.g. `s3://bucket/mindtoscript/models/v1.0.0/`).
- Do NOT store secrets in the repo. Use an EC2 instance role or Secrets Manager for production.
- If you use the HF hub, ensure network access and compatible `transformers` versions.
- For production inference use a GPU host (g4dn.xlarge) and the NVIDIA Container Toolkit.

Related docs & demos
--------------------
- `DESIGN.md` — architecture, timeline, S3/IAM guidance
- `notebooks/end_to_end_demo.ipynb` — synthetic end-to-end demo
- `scripts/run_demo_with_bart.py` — runs a real BART decoder (downloads model)

Troubleshooting
---------------
- If `git push` fails with SSH errors, either add your SSH key to GitHub or switch to HTTPS remote.
- If `transformers` fails to load models, ensure your Python + CUDA + torch versions match the HF wheel.
- If GPU OOM occurs, reduce batch size and enable mixed precision.

License & citation
------------------
Add your license file (LICENSE) and citation information for ZuCo and related papers.
