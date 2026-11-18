# Results: 206 FIO NVMe Sweep

**Main goal:** Measure fio IOPS, bandwidth, and latency across a yaml sweep  
**Tools:** Run the system fio executable with --output-format=json and libaio against yaml filename

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_146 · recorded 2026-11-18). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · AMD EPYC 9374F 32-Core Processor · Ubuntu 24.04.5 LTS · kernel 6.8.0-138-generic · driver 595.112.07
- **Run:** 2026-11-18T10:42:39Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:42 · benchmark 00:12 · whole run 00:59
- **Source:** run 206_20261118_104239
- **Command:** `bash run_benchmark.sh --smoke --io-engine libaio --filename data/fio_target.bin --direct 1 --rw randread --block-size 4k --size 64m --io-depth 1,8 --num-jobs 4 --warmup-duration-sec 0 --runtime 5`

| # | Metric | Result |
|---|---|---|
| 1 | IOPS mean at first sweep case (iops_mean) | 41537.092581 |
| 2 | Bandwidth at first sweep case, MB/s (bandwidth_mb_s) | 170.135931 |
| 3 | Completion latency p50 at first sweep case, us (clat_p50_us) | 81.408 |
| 4 | IOPS, 4k random read, iodepth 8 x 4 jobs = 32 in flight, every profile (iops_mean__randread_4k_qd8) | 323028.594281 |
<!-- /profile:smoke -->
