# Results: 320 rocSOLVER FP64 Dense LU Solve (single GPU)

**Main goal:** Measure sustained FP64 dense LU solve (getrf + getrs) throughput with rocSOLVER on one GPU  
**Tools:** Build src/rochpl.cpp (rocSOLVER LU) with scripts/build.sh, then run mpirun -np 1 bin/rochpl --N

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_139 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T14:15:03Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:15 · benchmark 00:03 · whole run 00:24
- **Source:** run 320_20261113_141503
- **Command:** `bash run_benchmark.sh --smoke --problem-size-N 4096 --warmup-iters 0 --num-iterations 1`

| # | Metric | Result |
|---|---|---|
| 1 | Mean rocSOLVER LU FP64, TFLOPS (sustained_fp64_performance_across_problem_size_n_tflops) | 0.0862405 |
| 2 | Mean rocSOLVER LU time, s (time_to_solution_across_problem_size_n_s) | 0.5313 |
| 3 | Mean percent of full MI300X FP64 matrix peak (percent_of_gpu_fp64_matrix_peak) | 0.05277876376988984 |
<!-- /profile:smoke -->
