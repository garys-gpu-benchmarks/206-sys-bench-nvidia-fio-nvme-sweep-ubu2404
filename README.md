# FIO NVMe Sweep Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://img.shields.io/badge/CI-host--safe-green.svg)](.github/workflows/ci.yml)

Target: Ubuntu 24.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/206-sys-bench-nvidia-fio-nvme-sweep-ubu2404.git
cd 206-sys-bench-nvidia-fio-nvme-sweep-ubu2404
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 24.04; NVIDIA; Python 3.12.3; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, Flexible I/O Tester (FIO), libaio. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path. direct: 1. Sweep dimensions: io_engine, filename, direct, sync_mode, rw, block_size, size, io_depth.

## 2. What It Validates

- Validates that each fio JSON job completes and parses IOPS, bandwidth, and clat percentiles. This is storage I/O, not a memory test
- #1: IOPS mean (iops_mean); is present and physically sensible.
- #2: Bandwidth, MB/s, by block size (bandwidth_mb_s); is present and physically sensible.
- #3: Completion latency p50, us (clat_p50_us) is present and physically sensible.

## 3. Metrics Captured

- **#1: IOPS mean** — stored as `iops_mean`.
- **#2: Bandwidth, MB/s, by block size** — stored as `bandwidth_mb_s`.
- **#3: Completion latency p50, us** — stored as `clat_p50_us`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 24.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, Flexible I/O Tester (FIO), libaio
- Python: Python 3.12.3

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path.

### GPU

Ubuntu 24.04 / NVIDIA / Bash, SQLite, Python, PyYAML, Flexible I/O Tester (FIO), libaio

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 24.04 |
| Kernel | kernel 6.8.0 |
| Python | Python 3.12.3 |
| ROCm | CUDA 12.8 |
| rocBLAS | N/A - rocBLAS not used |

Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path.

## 6. Installation

```bash
Run the system fio executable with --output-format=json and libaio against yaml filename
```

## 7. Running the Benchmark

```bash
Run the system fio executable with --output-format=json and libaio against yaml filename
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

fio JSON stdout plus a normalized CSV row per sweep point

sample_index,status,rw,block_size,io_depth,num_jobs,iops_mean,iops_p95,iops_p99,bandwidth_mb_s,clat_p50_us,clat_p99_us,clat_p999_us,p999_latency_ms,error_message
0,ok,randread,4k,1,4,80000,,,320,40,200,500,0.5,

```bash
Run the system fio executable with --output-format=json and libaio against yaml filename
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

fio JSON stdout plus a normalized CSV row per sweep point

sample_index,status,rw,block_size,io_depth,num_jobs,iops_mean,iops_p95,iops_p99,bandwidth_mb_s,clat_p50_us,clat_p99_us,clat_p999_us,p999_latency_ms,error_message
0,ok,randread,4k,1,4,80000,,,320,40,200,500,0.5,

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```
