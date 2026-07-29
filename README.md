# GPU-Accelerated Community Detection: Improving the Louvain Algorithm through Hybrid and Adaptive Methods

## Overview

Community detection is a fundamental problem in graph analytics with applications in social network analysis, biological networks, recommendation systems, and large-scale data mining. This project presents a set of optimizations for the **GPU-Accelerated Louvain Algorithm (GALA)** that address performance bottlenecks while maintaining community detection quality.

The work identifies three limitations in the original implementation:

- Poor memory access locality caused by arbitrary vertex numbering
- Cold-start overhead resulting from singleton community initialization
- Static kernel-selection thresholds that do not adapt to the input graph

To address these issues, three complementary enhancements are implemented:

- **N1:** Graph reordering using BFS traversal and degree-based sorting to improve GPU memory locality.
- **N2:** A GPU-resident Label Propagation (LPA) warm-start that provides a meaningful initial community assignment.
- **N3:** An adaptive kernel dispatch threshold computed from the 75th-percentile vertex degree at runtime.

Experiments were conducted on the **SNAP com-Amazon** graph using an **NVIDIA Tesla T4 GPU**.

---

## Project Authors

- **Aniket Jain (M25CS002)**
- **Ayush Pal (M25CS004)**
- **Ishan Bhati (M25CS006)**
- **Shrinidhi Narwriya (M25CS014)**

Department of Computer Science and Engineering  
Indian Institute of Technology Bhilai

Course: **CS516 / CSL516 – Parallelization of Programs**

---

# Methodology

## N1 — Graph Reordering

Graph reordering improves memory locality by renumbering vertices before executing the Louvain algorithm.

Two approaches are implemented:

### BFS Reordering
- Assigns vertex IDs according to Breadth-First Search traversal order.
- Improves spatial locality during GPU memory accesses.

### Degree-Sorted Reordering
- Renumbers vertices in ascending order of degree.
- Aligns graph layout with GALA's degree-based kernel scheduling.

---

## N2 — GPU LPA Warm-Start

Three iterations of Label Propagation are executed entirely on the GPU before Louvain begins.

This replaces singleton initialization with an initial partition that reduces the active vertex set during the first Louvain iteration.

---

## N3 — Adaptive Kernel Threshold

Instead of using compile-time degree thresholds, the implementation computes the **75th-percentile vertex degree** of the input graph at runtime and uses it as the warp/block dispatch boundary.

---

# Experimental Setup

## Hardware

- NVIDIA Tesla T4 GPU
- CUDA 12.8
- GCC 11.4
- Google Colaboratory

## Dataset

**SNAP com-Amazon Co-purchase Graph**

| Property | Value |
|----------|------:|
| Vertices | 334,863 |
| Edges | 925,872 |
| Graph Type | Undirected, Unweighted |
| Source | Stanford SNAP |

---

# Results

| Method | Average Speedup | Best Speedup | Average Modularity |
|--------|----------------:|-------------:|-------------------:|
| Baseline GALA | 1.00× | 1.00× | 0.9224 |
| N1a – BFS Reordering | 1.39× | 1.67× | 0.9225 |
| N1b – Degree Sorting | 1.42× | 1.76× | 0.9219 |
| N2 – GPU LPA Warm-Start | 1.19× | 1.38× | 0.9224 |
| N3 – Adaptive Threshold | 1.27× | 1.46× | 0.9219 |
| Combined Configuration | 1.17× | 1.35× | 0.9225 |

---

# Repository Structure

```
.
├── src/
├── include/
├── datasets/
├── scripts/
├── results/
├── README.md
└── LICENSE
```

---

# References

This work builds upon:

> Wang et al., **"Swift Unfolding of Communities: GPU-Accelerated Louvain Algorithm (GALA),"** ACM SIGPLAN Symposium on Principles and Practice of Parallel Programming (PPoPP), 2025.

The dataset used for evaluation is:

> SNAP – com-Amazon Co-purchase Network.

---
