# Mind-to-Script

CI status: ![CI](https://github.com/<your-org>/<your-repo>/actions/workflows/ci.yml/badge.svg)

Replace `<your-org>/<your-repo>` with your GitHub repository path to enable the badge.

See DESIGN.md for architecture and run instructions.

Quickstart — Docker, example run, and tests
------------------------------------------

Build the Docker image:

    docker compose build

Run the example inference (container runs scripts/run_example_inference.py and exits):

    # download model from S3 (optional) and run example
    MODEL_S3_URI=s3://bucket/path/ docker compose run --rm example

Or with docker directly:

    docker build -t mind-to-script:latest .
    docker run --rm -e RUN_EXAMPLE=1 -e MODEL_S3_URI="s3://bucket/path/" mind-to-script:latest

Run the API server (default) with docker-compose:

    docker compose up -d web
    # then test:
    curl -X POST http://127.0.0.1:8000/infer -H "Content-Type: application/json" -d '{"signals":[[0,0,0,0],[0,0,0,0]],"sfreq":500}'

Local tests (Windows PowerShell):

    .\.venv\Scripts\Activate.ps1
    .\run_tests.ps1

Local tests (WSL / macOS):

    source .venv/bin/activate
    ./run_tests.sh

Notes
- Place a trained bridge at `./models/bridge.pt` and/or a HF decoder folder under `./models/` to run end-to-end inference.
- If no local/S3 model is available, the container will attempt to download the BART decoder from the HF hub (network required).

# Neurolinked
