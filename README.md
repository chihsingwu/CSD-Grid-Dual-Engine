# Physical Boundary Validation for Grid AI

A compact open-source reference package for **physics-boundary validation** of AI systems in electric power grids.

This project does **not** present a full grid product or a replacement for existing grid AI models. It provides a small, reproducible experimental layer for testing whether AI predictions remain aligned with **dynamic stability physics** near weak-damping and critical-transition regimes.

The current reference implementation focuses on **Critical Slowing Down Early Warning Signals (CSD-EWS)** using the Kundur 4-machine system and a KA excitation-gain sweep.

<img width="1950" height="1350" alt="engine_ab_compare" src="https://github.com/user-attachments/assets/1da9f3ae-1930-42e0-a472-1abc36d17f65" />

## 🚀 Empirical Validation Results

### ⚡ The Core Breakthrough: Weak Damping & Dynamic Small-Signal Instability
Traditional grid monitoring relies heavily on **Voltage Deviation ($V_{dev}$)**. However, as power systems integrate massive renewable energy sources, a catastrophic vulnerability emerges: **dynamic small-signal instability under weak-damping conditions**. In these scenarios, excitation controls artificially maintain nominal voltage, masking the severe degradation of the system's underlying dynamic structure. **$V_{dev}$ fails completely here, while CSD-EWS thrives**.

---

### 📊 Experiment 1: IEEE 39-Bus Transient Stability
[cite_start]Validated using **ANDES v2.0.0** on the New England 39-bus system across **116 multi-fault scenarios** ($77$ stable, $39$ unstable) with a 60 Hz PMU-equivalent sampling rate[cite: 9, 10, 25].

#### 📈 Boundary Discovery & Performance
* [cite_start]**Critical Clearing Time (CCT) Discovery**: Systematically identified the exact physical bifurcation boundary for a Bus 16 fault between **1.3s (Stable)** and **1.4s (Unstable)**[cite: 27, 28, 29].
* [cite_start]**Pure Data-Driven Inference**: Operating **without any network topology, power flow models, or pre-trained classifiers**, Engine A achieved an exceptional **AUC-ROC of 0.8795** using rolling window metrics[cite: 38, 39].

| Metric Evaluation (Post-Fault Window) | AUC-ROC |
| :--- | :---: |
| [cite_start]**`score_J`** (Mean $\log \max|\lambda(J)|$ - Last 20 windows) [cite: 38] | [cite_start]**0.8795** [cite: 38] |
| [cite_start]**`score_trend`** (Rolling trend statistic slope) [cite: 38] | [cite_start]**0.8025** [cite: 38] |
| [cite_start]**`combo`** (Weighted Composite Score) [cite: 38] | [cite_start]**0.8768** [cite: 38] |

* [cite_start]**Clean Binary Separation**: In single-scenario validation, the rolling trend slope provides an instantaneous binary indicator—a **positive slope** triggers immediate critical alert routing, while a **negative slope** robustly tracks system post-fault recovery[cite: 36].

---

### 📉 Experiment 2: Kundur 4-Machine 19-Point $K_A$ Sweep (The Ultimate Proof)
[cite_start]To simulate the absolute blind spot of conventional AI models, we executed an intensive **19-point excitation gain ($K_A$) sweep** ($K_A = 5$ to $500$) to induce progressive inter-area oscillation damping degradation near the 0.6 Hz mode[cite: 357, 361, 366].

#### 🚨 Complete Dual-Engine Benchmark vs. Baseline
[cite_start]As the excitation system works harder to maintain voltage setpoints, traditional indicators point in the **perfect opposite direction ($\rho = -0.977$)**, signaling "improved safety" while the grid's dynamic structure is actually fracturing[cite: 363, 375, 376]. [cite_start]**Engine B completely reverses this failure with near-perfect correlation ($\rho = +0.993$)**[cite: 361, 362].

| Method / Indicator | Domain | Spearman Correlation ($\rho$) | $p$-value | Physical Verdict |
| :--- | :---: | :---: | :---: | :--- |
| **Engine B LFPR** (This Work) | [cite_start]**Spectral** [cite: 363] | [cite_start]**+0.993** [cite: 363] | [cite_start]**< 0.0001** [cite: 363] | [cite_start]**Near-Perfect Primary Indicator** [cite: 363] |
| **Combo (A+B, $0.5+0.5$)** | [cite_start]**Dual-Domain** [cite: 363] | [cite_start]**+0.974** [cite: 363] | [cite_start]**< 0.0001** [cite: 363] | [cite_start]**Excellent Robust Combination** [cite: 363] |
| **Engine B -Entropy** | [cite_start]**Spectral** [cite: 363] | [cite_start]**-0.930** [cite: 363] | [cite_start]**< 0.0001** [cite: 363] | [cite_start]**Strong (Entropy decays as danger rises)** [cite: 363] |
| **Engine A `score_J`** | [cite_start]**Time** [cite: 363] | [cite_start]**+0.730** [cite: 363] | [cite_start]**0.0004** [cite: 363] | [cite_start]**Significant Complementary Role** [cite: 363] |
| *Vdev (Traditional Baseline)* | [cite_start]*Voltage* [cite: 363] | [cite_start]*-0.977* [cite: 363] | [cite_start]*< 0.0001* [cite: 363] | [cite_start]❌ **Complete Systematic Failure** [cite: 363] |

#### 🎯 Why Engine B Achieves Mathematical Supremacy ($\rho = +0.993$)
[cite_start]Rooted in **Critical Slowing Down (CSD) theory** (Scheffer et al., Nature 2009; Podolsky & Turitsyn, 2013), as a dynamical system approaches bifurcation, energy systematically concentrates into low-frequency modes[cite: 368, 369]. 

[cite_start]Our spectral engine continuously extracts the **Low-Frequency Power Ratio (LFPR)** within a fixed $[0, 2.0\text{ Hz}]$ boundary via an optimized 120-sample FFT window[cite: 388, 389, 395]:

$$\text{LFPR} = \frac{\sum |F(f)|^2 \text{ for } 0 < f \le 2.0\text{ Hz}}{\sum |F(f)|^2 \text{ for all } f}$$

[cite_start]As $K_A$ scales up and damping ratio drops to negative regimes, the 0.6 Hz inter-area oscillation energy grows monotonically relative to total signal energy[cite: 367, 372]. [cite_start]Engine B captures this physical redistribution with absolute precision, providing a zero-topology, PMU-only early warning system long before physical threshold violations manifest[cite: 372, 401].

---

### 🔄 The Power of the Dual-Engine Architecture
Our framework does not rely on a single analytical domain. [cite_start]Instead, it deploys **Engine A** and **Engine B** in parallel to achieve comprehensive, multi-domain physics validation near the critical stability boundary, eliminating blind spots across all primary power system instability modes[cite: 380, 381, 382].

### 🤝 Cross-Domain Physical Complementarity
| Instability Type | Physical Mechanism | Best Indicator | Rationale |
| :--- | :--- | :---: | :--- |
| **Transient Stability** (Large Disturbance) | [cite_start]Nonlinear angle divergence  | [cite_start]**Engine A (`score_J`)**  | [cite_start]Jacobian captures rapid, highly nonlinear state transitions  |
| **Small-Signal Stability** (Weak Damping) | [cite_start]Low-freq oscillation growth  | [cite_start]**Engine B (`LFPR`)**  | [cite_start]Fourier directly measures energy redistribution into target modes  |
| **Voltage Collapse** | [cite_start]Saddle-node bifurcation  | [cite_start]**Engine A ($\min\|\lambda\|$)**  | [cite_start]Dynamic Jacobian singularity detection  |
| **Inter-Area Oscillation** (0.1–2 Hz) | [cite_start]Mode undamping  | [cite_start]**Engine B (`LFPR`)**  | [cite_start]Direct spectral measurement of target frequency  |

> [cite_start]🎯 **Dual-Engine Synergy**: Engine A and Engine B serve as independent physical implementations of Critical Slowing Down (CSD) theory[cite: 382]. By cross-checking time-domain eigenvalue trajectories against frequency-domain damping rates simultaneously, the system eliminates false positives and robustly flags anomalies before they physically manifest in the grid.





## Core idea

Modern grid AI methods can be evaluated not only by classification accuracy or OPF feasibility, but also by whether their outputs remain consistent with physical stability margins near dynamic boundaries.

This package frames that validation in three layers:

### Layer 1 — Physical scenario generation

Inspired by recent LLM-driven transient stability assessment workflows, complex power-system scenarios can be generated through simulation tools such as ANDES.

Examples include:

* clearing-time sweeps,
* excitation-gain sweeps,
* weak-damping regimes,
* renewable-penetration stress cases,
* line, bus, or generator disturbance scenarios.

In this package, the reference demo uses a Kundur 4-machine KA sweep as a controlled physical boundary case.

### Layer 2 — CSD-EWS dynamic safety margin

The package computes interpretable early-warning indicators that quantify whether a system is approaching dynamic instability.

The current dual-engine implementation includes:

* **Engine A — Time-domain Jacobian proxy**  
Estimates a local linear transition operator from multichannel PMU-like trajectories and summarizes the eigenvalue trajectory.
* **Engine B — Spectral CSD indicator**  
Computes low-frequency power ratio (LFPR) and spectral entropy from sliding FFT windows.

These indicators act as a **damping-aware physical scale** for weak-damping and low-frequency oscillation regimes.

### Layer 3 — AI prediction cross-validation interface

The intended next step is a standardized interface where any AI system can submit predictions for the same physical scenarios.

Examples of candidate AI systems include:

* grid-native models such as GridSFM-style approaches,
* cross-domain physical foundation models such as TokaMind-style approaches,
* LLM-generated TSA classifiers,
* conventional CNN/LSTM/Transformer stability classifiers.

Their predictions can then be cross-checked against CSD-EWS indicators, damping trends, LFPR, and instability ground truth.

The goal is not to claim that CSD-EWS replaces these models. The goal is to provide a **physics-informed validation layer**:

> Does the AI prediction remain consistent with damping-aware stability evidence near the physical boundary? By establishing this cross-validation interface, we enable rigorous benchmarking of diverse AI architectures—ranging from grid-native foundation models like GridSFM to cross-domain models like TokaMind—against the immutable physical laws governing dynamic stability.

## What this package currently provides

This v0.1 release provides the CSD-EWS reference layer only:

* Engine A: data-driven Jacobian proxy score.
* Engine B: LFPR and spectral entropy score.
* Kundur KA sweep demo using ANDES.
* Spearman correlation analysis against inter-area oscillation amplitude.
* Reproducible figure and data export.

It is intentionally small. It does not include a database, web service, Docker stack, CI pipeline, or full AI benchmark server.

## Installation

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\\Scripts\\activate
pip install -e .
```

Core engine dependencies:

```bash
pip install numpy scipy matplotlib
```

For the Kundur simulation demo, install ANDES:

```bash
pip install andes openpyxl
```

## Quick smoke test

```bash
pytest -q
```

## Run Kundur KA sweep

Small sweep:

```bash
csd-dual-engine --ka-values 10,50,200,500 --output-dir results\_demo
```

Full default 19-point sweep:

```bash
csd-dual-engine --output-dir results
```

Expected outputs:

```text
results/
├─ kundur\_engine\_ab\_summary.csv
├─ kundur\_engine\_ab.npy
└─ engine\_ab\_compare.png
```

## Programmatic use

```python
import numpy as np
from csd\_dual\_engine import engine\_a\_jacobian, engine\_b\_spectral

t = np.arange(0, 15, 1/60)
y = np.column\_stack(\[
    np.sin(2\*np.pi\*0.5\*t),
    np.cos(2\*np.pi\*0.8\*t),
])

a = engine\_a\_jacobian(t, y, n\_vars=2)
b = engine\_b\_spectral(t, y, fs=60.0)

print(a.log\_max\_eigenvalue\_score)
print(b.lfpr\_score, b.spectral\_entropy\_score)
```

## Example interpretation

In a weak-damping KA sweep, voltage deviation may decrease because excitation control keeps voltage regulated, even as inter-area oscillation damping deteriorates.

CSD-EWS is designed to catch this type of mismatch:

* voltage can look acceptable,
* damping can still worsen,
* LFPR or Jacobian-proxy indicators can expose the dynamic boundary.

This makes the package useful as a **validation reference** for AI models that claim to understand grid stability.

## Intended research use

Use this package to:

* generate or analyze controlled physical-boundary cases,
* compute damping-aware CSD indicators,
* compare AI predictions against interpretable physical evidence,
* build small benchmark notes for grid AI evaluation,
* support technical discussions around physical AI reliability.

Do not overinterpret this release as:

* a complete PMU production system,
* a certified protection relay,
* a replacement for full power-system studies,
* a complete benchmark for all grid AI models.

## Notes

* The Kundur demo requires ANDES and its bundled `kundur\_full.xlsx` case.
* Engine A uses a PCA-reduced local linear transition operator, then summarizes the final post-fault eigenvalue trajectory.
* Engine B computes LFPR and spectral entropy over sliding windows.
* The package intentionally stays small: no database, no web server, no Docker, no CI by default.

## Citation / attribution

TaiScience Research Group — Open Source Reference Implementation.

If you use this package, please cite or acknowledge:

```text
TaiScience Research Group. Physical Boundary Validation for Grid AI: CSD-EWS Dual-Engine Reference Implementation, 2026.
```

## 

