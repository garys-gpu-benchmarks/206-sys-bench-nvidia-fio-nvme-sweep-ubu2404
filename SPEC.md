# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path. direct: 1. Sweep dimensions: io_engine, filename, direct, sync_mode, rw, block_size, size, io_depth.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| io_engine | `--io-engine` | smoke=libaio, baseline=libaio, extended=libaio | libaio | From Parameter list; see Execution Description With Parameters. |
| filename | `--filename` | smoke=data/fio_target.bin, baseline=data/fio_target.bin, extended=data/fio_target.bin | data/fio_target.bin | From Parameter list; see Execution Description With Parameters. |
| direct | `--direct` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| sync_mode | `--sync-mode` | smoke=async, baseline=async, extended=async | async | From Parameter list; see Execution Description With Parameters. |
| rw | `--rw` | smoke=randread, baseline=randread,randwrite, extended=randread,randwrite,read,write | randread,randwrite | From Parameter list; see Execution Description With Parameters. |
| block_size | `--block-size` | smoke=4k, baseline=4k,8k, extended=4k,8k,128k | 4k,8k | From Parameter list; see Execution Description With Parameters. |
| size | `--size` | smoke=64m, baseline=1g, extended=4g | 1g | From Parameter list; see Execution Description With Parameters. |
| io_depth | `--io-depth` | smoke=1,8, baseline=1,8,32, extended=1,8,32,64 | 1,8,32 | From Parameter list; see Execution Description With Parameters. |
| num_jobs | `--num-jobs` | smoke=4, baseline=4, extended=4 | 4 | From Parameter list; see Execution Description With Parameters. |
| warmup_duration_sec | `--warmup-duration-sec` | smoke=0, baseline=2, extended=1 | 2 | From Parameter list; see Execution Description With Parameters. |
| runtime | `--runtime` | smoke=5, baseline=15, extended=12 | 15 | From Parameter list; see Execution Description With Parameters. |
| time_based | `--time-based` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| stonewall | `--stonewall` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run the system fio executable with --output-format=json and libaio against yaml filename
```

## Raw Output Format

fio JSON stdout plus a normalized CSV row per sweep point

sample_index,status,rw,block_size,io_depth,num_jobs,iops_mean,iops_p95,iops_p99,bandwidth_mb_s,clat_p50_us,clat_p99_us,clat_p999_us,p999_latency_ms,error_message
0,ok,randread,4k,1,4,80000,,,320,40,200,500,0.5,

## Metrics

- **#1: IOPS mean** — stored as `iops_mean`.
- **#2: Bandwidth, MB/s, by block size** — stored as `bandwidth_mb_s`.
- **#3: Completion latency p50, us** — stored as `clat_p50_us`.

## Framework

Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path.

## Installation and Execution Summary

Run apt-installed fio --output-format=json --ioengine=libaio against yaml filename (default data/fio_target.bin), expanding the cartesian yaml rw x block_size x io_depth sweep with size, num_jobs, and runtime, to measure IOPS, bandwidth, and completion-latency percentiles. This is file-backed storage I/O, not a DRAM microbenchmark

## Platform Portability

- **AMD (primary):** ```bash
Run the system fio executable with --output-format=json and libaio against yaml filename
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

fio JSON stdout plus a normalized CSV row per sweep point

sample_index,status,rw,block_size,io_depth,num_jobs,iops_mean,iops_p95,iops_p99,bandwidth_mb_s,clat_p50_us,clat_p99_us,clat_p999_us,p999_latency_ms,error_message
0,ok,randread,4k,1,4,80000,,,320,40,200,500,0.5,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Cartesian fio sweep writing JSON jobs against a file target (default data/fio_target.bin), not necessarily a raw /dev/nvme device. io_engine: libaio. filename: target path.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
