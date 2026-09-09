# Benchmarks

Every measured experiment belongs here, including experiments that fail or
produce slower results.

## Rules

- Write the prediction before running the experiment.
- Change only one performance variable at a time.
- Record the exact hardware and configuration.
- Always include units.
- Never delete an inconvenient result.
- Link each result to its commit when possible.

## Experiment Log

| Date | Week | Change | Hardware | Configuration | Prediction | Result | Memory | Notes |
|---|---:|---|---|---|---|---|---|---|
| 2026-09-09 | 1 | Sequential memory read, 1 thread | Intel i5-1135G7, WSL2 | sysbench 1.0.20; 64 MiB block; 100 GiB transferred; local scope | Baseline; initial overall maximum prediction: 40 GB/s | 12.97, 13.30, 13.14 GB/s; average 13.14 GB/s | 64 MiB allocated per run | Run 1 had more background applications open |
| 2026-09-09 | 1 | Sequential memory read, 2 threads | Intel i5-1135G7, WSL2 | sysbench 1.0.20; 64 MiB/thread; 100 GiB transferred; local scope | 23 GB/s | 22.76, 23.50, 23.41 GB/s; average 23.22 GB/s | 128 MiB allocated per run | Achieved 1.77x the 1-thread average |
| 2026-09-09 | 1 | Sequential memory read, 4 threads | Intel i5-1135G7, WSL2 | sysbench 1.0.20; 64 MiB/thread; 100 GiB transferred; local scope | 46 GB/s | 34.74, 37.80, 37.78 GB/s; average 36.77 GB/s | 256 MiB allocated per run | Scaling weakened as threads shared memory bandwidth |
| 2026-09-09 | 1 | Sequential memory read, 8 threads | Intel i5-1135G7, WSL2 | sysbench 1.0.20; 64 MiB/thread; 100 GiB transferred; local scope | 44 GB/s after lower-thread results | 44.11, 48.29, 47.57 GB/s; average 46.66 GB/s | 512 MiB allocated per run | Best result 48.29 GB/s, 70.7% of theoretical bandwidth |