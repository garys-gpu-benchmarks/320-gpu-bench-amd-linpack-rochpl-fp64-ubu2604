# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Builds local bin/rochpl (rocHPL) with a hipcc GCC pin, then runs mpirun --allow-run-as-root -np num_gpus bin/rochpl with CLI --N --NB --P --Q --pfact. problem_size_N >=65536 is clamped to 32768. num_iterations is capped by timed_repeats (6 at N>=32768 baseline, 16 extended). Residual Check: PASSED is required. Sweep dimensions: num_gpus, process_grid_P, process_grid_Q, precision, problem_size_N, block_size, panel_factorization, warmup_iters.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| num_gpus | `--num-gpus` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| process_grid_P | `--process-grid-p` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| process_grid_Q | `--process-grid-q` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| precision | `--precision` | smoke=fp64, baseline=fp64, extended=fp64 | fp64 | From Parameter list; see Execution Description With Parameters. |
| problem_size_N | `--problem-size-n` | smoke=4096, baseline=32768, extended=32768 | 32768 | From Parameter list; see Execution Description With Parameters. |
| block_size | `--block-size` | smoke=256, baseline=384, extended=512 | 384 | From Parameter list; see Execution Description With Parameters. |
| panel_factorization | `--panel-factorization` | smoke=2, baseline=2, extended=2 | 2 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=1, baseline=6, extended=16 | 6 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Build scripts/build.sh under hipcc_host_gcc.sh; run mpirun bin/rochpl with CLI --N --NB --P --Q --pfact. HPL.dat is present but is not the control path
```

## Raw Output Format

Local bin/rochpl stdout plus a normalized CSV aggregate. This is not mpirun and not an HPC container log

sample_index,status,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_gpu_fp64_matrix_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,12.5,7.6,8.4,

## Metrics

- **#1: Mean rocSOLVER LU FP64, TFLOPS** — stored as `sustained_fp64_performance_across_problem_size_n_tflops`.
- **#2: Mean rocSOLVER LU time, s** — stored as `time_to_solution_across_problem_size_n_s`.
- **#3: Mean percent of full MI300X FP64 matrix peak** — stored as `percent_of_gpu_fp64_matrix_peak`.

## Framework

Builds local bin/rochpl (rocHPL) with a hipcc GCC pin, then runs mpirun --allow-run-as-root -np num_gpus bin/rochpl with CLI --N --NB --P --Q --pfact. problem_size_N >=65536 is clamped to 32768. num_iterations is capped by timed_repeats (6 at N>=32768 baseline, 16 extended).

## Installation and Execution Summary

Build and run local bin/rochpl under mpirun --allow-run-as-root for yaml warmup plus timed repeats, parse GFLOPS, time-to-solution, and Residual Check, and abort if Residual Check is not PASSED, to measure sustained FP64 Linpack. Clamp problem_size_N from >=65536 to 32768

## Platform Portability

- **AMD (primary):** ```bash
Build scripts/build.sh under hipcc_host_gcc.sh; run mpirun bin/rochpl with CLI --N --NB --P --Q --pfact. HPL.dat is present but is not the control path
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

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

Local bin/rochpl stdout plus a normalized CSV aggregate. This is not mpirun and not an HPC container log

sample_index,status,sustained_fp64_performance_across_problem_size_n_tflops,percent_of_gpu_fp64_matrix_peak,time_to_solution_across_problem_size_n_s,error_message
0,ok,12.5,7.6,8.4,

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
5. All required aggregate metrics are physically sensible (positive values). Builds local bin/rochpl (rocHPL) with a hipcc GCC pin, then runs mpirun --allow-run-as-root -np num_gpus bin/rochpl with CLI --N --NB --P --Q --pfact. problem_size_N >=65536 is clamped to 32768. num_iterations is capped by timed_repeats (6 at N>=32768 baseline, 16 extended).
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Builds local bin/rochpl (rocHPL) with a hipcc GCC pin, then runs mpirun --allow-run-as-root -np num_gpus bin/rochpl with CLI --N --NB --P --Q --pfact. problem_size_N >=65536 is clamped to 32768. num_iterations is capped by timed_repeats (6 at N>=32768 baseline, 16 extended).

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
