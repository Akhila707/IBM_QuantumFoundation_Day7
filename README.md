# IBM Quantum Foundations — Day 7

<div align="center">

![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-052FAD?style=flat-square&logo=ibm&logoColor=white)
![ibm_torino](https://img.shields.io/badge/ibm__torino-133%20qubits-4fc3f7?style=flat-square)
![Qiskit](https://img.shields.io/badge/Qiskit-6929C4?style=flat-square&logoColor=white)
![Python 3.10](https://img.shields.io/badge/Python%203.10-1a1a2e?style=flat-square&logo=python&logoColor=4fc3f7)
![Day 7](https://img.shields.io/badge/Day%2007-Complete-4fc3f7?style=flat-square)
![Day 8](https://img.shields.io/badge/Day%2008-Loading...-555555?style=flat-square)

</div>

<br/>

<div align="center">
<i>Part of the IBM Quantum 20-Day Learning Sprint · VIT Chennai</i>
</div>

---

## Overview

This notebook runs all five experiments from Days 1–6 on real IBM quantum hardware — **ibm_torino**, a 133-qubit Heron r1 processor. Each result is compared directly against the AerSimulator baseline to quantify the effect of real hardware noise.

Every job ID is verifiable on quantum.ibm.com.

---

## Hardware

| Property | Value |
|----------|-------|
| Backend | ibm_torino |
| Architecture | Heron r1 |
| Qubits | 133 |
| Location | us-east |
| Connection | ibm_quantum_platform channel |
| Shots per experiment | 1000 |

---

## Results

### Experiment 1 · Bell State

A two-qubit entangled state. The ideal result contains only `|00⟩` and `|11⟩`. Any `|01⟩` or `|10⟩` counts are direct evidence of gate and readout error.

| State | Simulator | ibm_torino |
|-------|-----------|------------|
| \|00⟩ | 500 | 504 |
| \|11⟩ | 500 | 402 |
| \|01⟩ | 0 | 52 |
| \|10⟩ | 0 | 42 |

```
Accuracy  :  90.6%
Noise     :   9.4%
Job ID    :  d6rpa9cu243c73a1mml0
```

---

### Experiment 2 · Superposition

A single-qubit H gate. The ideal result is 50% `|0⟩` and 50% `|1⟩`. Hardware shows a slight readout bias toward `|0⟩`.

| State | Simulator | ibm_torino |
|-------|-----------|------------|
| \|0⟩ | 476 | 533 |
| \|1⟩ | 524 | 467 |

```
Accuracy  :  97.0%
Noise     :   3.0%
Job ID    :  d6rpb820q0ls73ctnchg
```

The shallow circuit depth (one gate) results in the lowest noise of all five experiments.

---

### Experiment 3 · Grover's Algorithm

Searching for `|11⟩` among four states using one iteration of Grover's. The simulator finds the target in 100% of measurements. On hardware, the target still dominates but noise redistributes counts to incorrect states.

| State | Simulator | ibm_torino |
|-------|-----------|------------|
| \|11⟩ | 1000 | 837 |
| \|01⟩ | 0 | 76 |
| \|10⟩ | 0 | 74 |
| \|00⟩ | 0 | 13 |

```
Accuracy  :  83.7%
Noise     :  16.3%
Job ID    :  d6rpc3bopkic73fjes9g
```

The deeper circuit (8 gates) accumulates more error than the Bell State or Superposition experiments.

---

### Experiment 4 · VQE

Variational Quantum Eigensolver minimizing the Pauli-Z Hamiltonian. The COBYLA optimizer was initialized from the simulator-optimal parameter to reduce the number of hardware jobs required.

| Metric | Simulator | ibm_torino |
|--------|-----------|------------|
| Ground state energy | −1.0000 | −0.8440 |
| Optimal θ | 3.1721 rad | 3.172 rad |
| Iterations | 14 | 10 |
| True value | −1.0000 | −1.0000 |

```
Accuracy  :  84.4%
Noise     :  15.6%
```

Hardware noise prevents the optimizer from reaching the exact ground state. The energy landscape is distorted by gate errors, causing the optimizer to converge at a suboptimal point.

---

### Experiment 5 · QAOA MaxCut

Quantum Approximate Optimization Algorithm on a 4-node square graph. The target partition is `{0,2}` vs `{1,3}`, cutting all 4 edges.

| Metric | Simulator | ibm_torino |
|--------|-----------|------------|
| Best avg cut | 2.984 | 2.812 |
| Max possible cut | 4 | 4 |
| Optimal solution | 0101 (281 counts) | 0101 (218 counts) |

```
Accuracy  :  70.3%
Noise     :  29.7%
```

Despite the highest noise of all five experiments, the optimal partition `0101` remained the most frequent outcome. The correct answer still emerged from the noisy hardware.

---

## Summary

| Experiment | Accuracy | Noise | Circuit complexity |
|------------|----------|-------|--------------------|
| Superposition | 97.0% | 3.0% | 1 gate · 1 qubit |
| Bell State | 90.6% | 9.4% | 2 gates · 2 qubits |
| VQE | 84.4% | 15.6% | parametric · 1 qubit |
| Grover's | 83.7% | 16.3% | 8 gates · 2 qubits |
| QAOA | 70.3% | 29.7% | 12+ gates · 4 qubits |

Circuit depth is the primary driver of noise accumulation. Shallower circuits produce results closer to the theoretical ideal. This is the defining constraint of NISQ-era quantum hardware and the motivation for the error mitigation techniques covered in Day 6.

---

## Key Observation

All five experiments produced results that agree qualitatively with theory — the correct answers dominated in every case. The deviation from ideal values is consistent with the known error rates of superconducting qubit processors. This confirms that near-term quantum hardware can produce meaningful results for small-scale problems, provided circuit depth is managed carefully.

---

## Tech Stack

```python
qiskit                  >= 1.0.0
qiskit-aer              >= 0.17.2
qiskit-ibm-runtime      >= 0.45.1
scipy                   >= 1.10.0
numpy                   >= 1.24.0
matplotlib              >= 3.7.0
python-dotenv           >= 1.0.0
```

---

## Setup

```bash
git clone https://github.com/Akhila707/IBM_QuantumFoundation_Day7.git
cd IBM_QuantumFoundation_Day7
pip install -r requirements.txt
```

An IBM Quantum account and API token are required to reproduce hardware results. Set up your `.env` file:

```
IBM_QUANTUM_TOKEN=your_token_here
IBM_QUANTUM_CRN=crn:v1:bluemix:...
```

Results from AerSimulator are reproducible without a token.

---

## Project Structure

```
IBM_QuantumFoundation_Day7/
│
├── notebooks/
│   └── 01_Noise_real_hardware.ipynb
│
├── results/
│   ├── simulator_vs_hardware.png
│   └── day7_summary.png
│
├── .env                 ← credentials (not committed)
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Sprint Progress

```
Day 01  ──  ✅  Qiskit setup · Hello Quantum · first IBM cloud circuit
Day 02  ──  ✅  Superposition · Entanglement · Multi-gate circuits
Day 03  ──  ✅  Gates deep-dive · Grover's algorithm
Day 04  ──  ✅  VQE · parametric circuits · COBYLA optimizer
Day 05  ──  ✅  QAOA · MaxCut · optimal partition found
Day 06  ──  ✅  Quantum Error Mitigation · noise models · ZNE
Day 07  ──  ✅  All 5 experiments on real IBM hardware · ibm_torino
Day 08  ──  ⬡   QML · QSVM · ZZFeatureMap · Iris dataset
·
·
Day 20  ──  ·   Final push · 50+ applications · LinkedIn article
```

---

## Security

- Credentials stored in `.env`, excluded from version control via `.gitignore`
- Token and CRN are loaded at runtime via `python-dotenv`
- No credentials appear in any notebook cell output

---

<div align="center">

[![GitHub](https://img.shields.io/badge/Akhila707-181717?style=flat-square&logo=github)](https://github.com/Akhila707)
&nbsp;·&nbsp;
[![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-052FAD?style=flat-square&logo=ibm&logoColor=white)](https://quantum.ibm.com)

</div>
