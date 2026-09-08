# Week 01: Roofline by Hand

# Week 1 — CPU Performance and the Roofline Model

## Day 1 — Performance Fundamentals

**Date:** 2026-09-08

### Goal

Understand how CPU performance is described and learn the basic ideas behind the Roofline performance model before writing or benchmarking code.

---

## 1. FLOP and FLOP/s

A **FLOP** is one floating-point operation, such as:

- Addition
- Subtraction
- Multiplication
- Division

**FLOP/s** measures how many floating-point operations are performed per second.

Therefore:

- FLOP measures an amount of computational work.
- FLOP/s measures a rate of computation.
- 1 GFLOP/s means one billion floating-point operations per second.

This follows the general rule that a quantity divided by time represents a rate.

---

## 2. Dot-Product Operation Count

A dot product of two vectors of length \(N\) requires:

- \(N\) multiplications
- \(N-1\) additions

The exact operation count is:

\[
N+(N-1)=2N-1
\]

For large \(N\), this is approximated as:

\[
2N\text{ FLOPs}
\]

---

## 3. Matrix-Multiplication Operation Count

Multiplying two \(N \times N\) matrices produces \(N^2\) output values.

Each output value is a dot product requiring approximately \(2N\) FLOPs.

Therefore:

\[
N^2 \times 2N = 2N^3
\]

The approximate work performed by square matrix multiplication is:

\[
\boxed{2N^3\text{ FLOPs}}
\]

The exact count is \(2N^3-N^2\), but \(2N^3\) is normally used for performance calculations.

---

## 4. CPU Hardware

My CPU is an Intel Core i5-1135G7 with:

- 4 physical cores
- 8 logical CPUs or hardware threads
- 2 hardware threads per physical core
- 2.40 GHz listed base frequency
- AVX2 and AVX-512 support

A physical core contains hardware that executes instructions.

A software thread is a sequence of instructions being executed. Hardware threads allow one physical core to keep track of more than one instruction stream. Two hardware threads do not create two physical cores because they share the core’s execution resources.

Each physical core can operate at its own clock frequency. Four cores running at 2.4 GHz do not create a single 9.6 GHz processor; they provide four separate streams of approximately 2.4 billion cycles per second.

---

## 5. SIMD and FMA

SIMD allows one instruction to operate on several numbers simultaneously.

An AVX-512 register contains 512 bits. An FP32 number occupies 32 bits:

\[
512/32=16
\]

Therefore, one AVX-512 instruction can operate on 16 FP32 values simultaneously.

FMA means fused multiply-add:

\[
a\times b+c
\]

This counts as two FLOPs for each number:

- One multiplication
- One addition

One AVX-512 FP32 FMA therefore performs:

\[
16\times2=32\text{ FLOPs}
\]

---

## 6. Estimated CPU Compute Ceiling

Using the idealized assumptions of:

- 4 physical cores
- 2.4 billion cycles per second
- 2 AVX-512 FMA instructions per core per cycle
- 16 FP32 values per instruction
- 2 FLOPs per FP32 FMA

The estimated theoretical ceiling is:

\[
4\times2.4\times2\times16\times2
=614.4\text{ GFLOP/s}
\]

\[
\boxed{P_{\text{compute}}\approx614.4\text{ GFLOP/s}}
\]

This is an idealized theoretical ceiling, not expected real-world performance. Real performance may be lower because of frequency changes, instruction throughput, memory access, cache misses, power limits, thermal limits, software implementation and other overheads.

---

## 7. Memory Hierarchy

Computer memory is arranged as a hierarchy:

1. CPU registers — smallest and fastest
2. L1 cache
3. L2 cache
4. L3 cache
5. RAM
6. SSD or hard drive — largest and slowest

RAM is fast compared with permanent storage but slow compared with CPU caches and registers.

Memory bandwidth measures the rate at which data can be transferred:

\[
\text{Bandwidth}
=
\frac{\text{bytes moved}}{\text{time}}
\]

It is commonly measured in GB/s.

---

## 8. Estimated Memory Bandwidth

The laptop has:

- 16 GiB of physical SK Hynix memory
- LPDDR4x-4267 memory
- An effective 128-bit or 16-byte-wide memory interface

The ideal theoretical bandwidth estimate is:

\[
4267\times10^6\times16
=
68.272\times10^9\text{ bytes/s}
\]

\[
\boxed{B_{\text{theoretical}}\approx68.3\text{ GB/s}}
\]

This is a hardware estimate. Actual usable bandwidth must be measured with a benchmark.

WSL currently has approximately 8 GiB available even though the Windows machine has 16 GiB of physical memory.

---

## 9. Arithmetic Intensity

Arithmetic intensity describes how much computation is performed for every byte moved:

\[
I=
\frac{\text{FLOPs performed}}{\text{bytes moved}}
\]

Its unit is FLOPs/byte.

For example:

\[
800\text{ FLOPs}/200\text{ bytes}
=
4\text{ FLOPs/byte}
\]

---

## 10. Compute-Bound and Memory-Bound Programs

A program is **memory-bound** when data movement limits performance.

A program is **compute-bound** when the processor’s calculation capacity limits performance.

The Roofline equation is:

\[
\boxed{
P=\min(P_{\text{compute}},B\times I)
}
\]

Where:

- \(P\) is attainable performance.
- \(P_{\text{compute}}\) is the compute ceiling.
- \(B\) is memory bandwidth.
- \(I\) is arithmetic intensity.
- \(B\times I\) is the memory-performance ceiling.

The smaller ceiling determines the bottleneck.

---

## 11. Data Reuse

Data reuse means loading a value from RAM, retaining it in cache or a register, and using it multiple times.

More reuse means:

- Fewer repeated transfers from RAM
- More calculations per byte moved
- Higher arithmetic intensity
- A better chance of becoming compute-bound

Matrix multiplication offers significant data reuse because values from matrices \(A\) and \(B\) participate in multiple output calculations.

Under an idealized minimum-traffic model for FP32 square matrix multiplication:

\[
\text{FLOPs}\approx2N^3
\]

\[
\text{bytes}\approx3N^2\times4=12N^2
\]

Therefore:

\[
I\approx\frac{2N^3}{12N^2}
=\frac{N}{6}\text{ FLOPs/byte}
\]

For \(N=120\):

\[
I\approx120/6=20\text{ FLOPs/byte}
\]

This model assumes nearly perfect reuse. A naive implementation may move much more data and achieve much lower performance.

---

## Day 1 Summary

Today I learned that performance is not determined by CPU frequency alone. It depends on cores, cycles, instruction throughput, SIMD width, FMA capability, memory bandwidth, arithmetic intensity and data reuse.

The main principle is:

> Predict the performance bottleneck before measuring the program.

### Provisional machine ceilings

- Estimated FP32 compute ceiling: **614.4 GFLOP/s**
- Estimated theoretical memory bandwidth: **68.3 GB/s**
- Actual compute and memory performance: **not measured yet**