# Week 01: Roofline by Hand

## Status

Not started.

## Predictions

Complete this section before running any performance measurement.

- Theoretical peak FP32 performance:
- Estimated memory bandwidth:
- Predicted naive matrix-multiplication performance:
- Predicted percentage of theoretical peak:
- Predicted primary bottleneck:

## Goal

Learn to predict CPU performance from first principles and understand why
naive matrix multiplication reaches only a small fraction of theoretical peak.

## Topics

- FLOPs and FLOP/s
- Memory bandwidth
- Arithmetic intensity
- Compute-bound versus memory-bound workloads
- The roofline model
- Honest benchmarking

## Machine Notes

- CPU: Intel Core i5-1135G7
- Physical cores: 4
- Threads per core: 2
- Logical CPUs detected: 8
- `nproc` available count: 7
- L3 cache: 8 MiB
- RAM available to WSL: 7.7 GiB
- Environment: Ubuntu 26.04 LTS under WSL2

### Open Question

Why does plain `nproc` report 7 while `nproc --all`, `lscpu`, and CPU affinity
all indicate 8 logical CPUs?

## Work Log

Record what was built, changed, and measured during the week.

## Results

Complete this section after all Week 1 measurements.

## What Surprised Me

Complete this section throughout the week.

## Completion Checklist

- [ ] Found the CPU's theoretical peak FLOP/s
- [ ] Found or measured memory bandwidth
- [ ] Implemented naive matrix multiplication in C
- [ ] Built a reusable timing harness
- [ ] Created a roofline plot
- [ ] Recorded every experiment in `BENCHMARKS.md`
- [ ] Explained why naive matrix multiplication misses peak performance