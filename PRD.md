# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
206

## Workload Name
FIO NVMe Sweep

## Execution Summary (Run and Measure)
Run apt-installed fio --output-format=json --ioengine=libaio against yaml filename (default data/fio_target.bin), expanding the cartesian yaml rw x block_size x io_depth sweep with size, num_jobs, and runtime, to measure IOPS, bandwidth, and completion-latency percentiles. This is file-backed storage I/O, not a DRAM microbenchmark

## Main Goal
Measure fio IOPS, bandwidth, and tail latency across a yaml sweep

## Validation Objective
Validates that each fio JSON job completes and parses IOPS, bandwidth, and clat percentiles. This is storage I/O, not a memory test

## Workload Category
Memory, Bandwidth & Data Movement

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
