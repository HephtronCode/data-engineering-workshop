# Data Engineering Workshop — Test Project

A minimal Python pipeline demonstrating argument parsing, `pandas` DataFrame creation, and Parquet output with `pyarrow`. Useful as a starter for data engineering exercises and Dockerized workflows.

## Overview

- Reads a `day` argument from the command line.
- Builds a small `pandas` DataFrame and prints its head.
- Writes a Parquet file named `output_day_<day>.parquet` to the working directory.

Core script: `pipeline.py`.

## Requirements

- Python >= 3.13
- `pandas >= 2.3.3`
- `pyarrow >= 23.0.0`
- Optional: [uv](https://github.com/astral-sh/uv) for fast, reproducible environments.

## Setup

### Option A: Using uv (recommended)

If you don’t have `uv` yet, follow the installation guide on the official repo.

1. From the project folder, create or update the lockfile (if missing):

```bash
uv lock
```

2. Create a virtual environment and install dependencies:

```bash
uv sync
```

This reads `pyproject.toml` and (optionally) `uv.lock` to install the exact dependencies.

### Option B: Using pip

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install pandas pyarrow
```

## Run Locally

Execute the pipeline for a given day (e.g., `3`):

```bash
python pipeline.py 3
```

You should see printed arguments, the DataFrame head, and a new file:

- `output_day_3.parquet`

Quick check of the output:

```bash
python -c "import pandas as pd; print(pd.read_parquet('output_day_3.parquet').head())"
```

## Run with Docker

This repo includes a `Dockerfile` that uses `uv` for dependency installation.

1. Build the image:

```bash
docker build -t de-workshop-test .
```

Note: The `Dockerfile` copies `pyproject.toml`, `uv.lock`, and `.python-version`. If `uv.lock` or `.python-version` are not present, either generate them (`uv lock`) or remove those lines from the `Dockerfile` before building.

2. Run the container, passing the `day` argument. Mount the current directory so the Parquet file is written to your host:

```bash
docker run --rm -v "$(pwd)":/app de-workshop-test 3
```

This uses the `ENTRYPOINT ["python", "pipeline.py"]` from the `Dockerfile`, so `3` is forwarded as the script argument.

## Project Layout

- `pipeline.py`: CLI script that builds a DataFrame and writes a Parquet file.
- `main.py`: Simple entrypoint printing a greeting (for quick sanity checks).
- `list_files.py`: Utility to list files in the current directory and print their contents.
- `pyproject.toml`: Project metadata and dependencies.
- `Dockerfile`: Container build using Python 3.13 slim and `uv`.
- `README.md`: This documentation.

## Troubleshooting

- Import errors or missing packages: Ensure your virtual environment is active or re-run dependency installation (`uv sync` or `pip install`).
- Docker build: If the build fails due to missing `uv.lock` or `.python-version`, generate the lockfile (`uv lock`) or adjust the `Dockerfile` COPY lines accordingly.
- Parquet write/read issues: Confirm `pyarrow` is installed and that the working directory is writable.

## Notes

- Python 3.13 is specified by the project and used in the Docker image.
- The demonstration DataFrame is tiny and for illustrative purposes only.

