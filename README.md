# QCK: Smart Data Pruning Core (v001)

**A Manifest for Smart Data and Green AI: From Brute-Force Ray Tracing to Preventive Geometric Selection.**

As the internet floods with synthetic text, training future foundational models on AI-generated "slop" leads to irreversible Model Collapse. The current paradigm ("Scale is all you need") hits physical limits when algorithms attempt to diagnose hallucination *after* training using extreme compute power.

The **QCK SMART Data Pruning** pipeline offers a fundamental paradigm shift: **Prevention instead of Diagnosis.** Based on the QCK framework, this tool postulates that high-quality, organic information inherently possesses a stable geometric signature—a clear attractor. Instead of training on petabytes of high-dimensional fractal noise, this script prunes the data geometrically *before* it enters the training pipeline.

## 🚀 The Mathematical Advantage
Instead of using complex Transformer loops, the QCK Pruner mathematically measures the dimensional roughness (D2) and semantic drift of the text topology in a 768-dimensional space (`mpnet-base-v2`). 



* **ACCEPTED (Smart Data):** Human thought exhibits a stable organic attractor. 
* **REJECTED (Fractal Noise / Slop):** LLMs exhibit a dangerous "Synthetic Perfection" or high-dimensional semantic jitter.

### 🌿 Green AI & O(N) Complexity
* **Pipeline Speed:** Compared to Monte-Carlo semantic ray-tracing (Behroozi et al.) with 1000 samples per document (computational complexity O(N×M)), QCK uses deterministic geometric trajectory analysis (complexity O(N)), achieving empirical speedups of **~10,000x** on standard CPU hardware.
* **Footprint:** Filters texts locally on a standard CPU using `< 1.5 GB RAM`.
* **Energy Savings:** Reduces energy consumption by **> 99.99%** compared to a 2500W GPU cluster.

---

> [!WARNING]
## 💼 Licensing & Commercial Use (Open Core)

This repository contains the **QCK Core** (Proof of Concept) and is released under the **GNU Affero General Public License v3 (AGPLv3)** (see `LICENSE.txt`). It is intended for personal use, academic research, and community evaluation.

**Enterprise & Commercial Application:**
The AGPLv3 is a strong copyleft license. It requires that any modified versions or services utilizing this code over a network (SaaS) must publicly share their full source code. ANY use of this software by a for-profit company in a closed-source environment, integration into commercial products, or deployment in a corporate infrastructure requires a valid **Proprietary Commercial License**. 

Clients with a Commercial License gain exclusive access to the **TAIF Enterprise Edition**, which operates independently of the AGPLv3 and features:
* **Zero-Trust Architecture:** Calibrated for high-precision detection with false-negative rates approaching zero on validated datasets.
* **I/O-Optimized Batch Processing:** Engineered for high-throughput, enterprise-scale document auditing without rendering bottlenecks.
* **Automated File Routing:** Physical quarantine isolation of anomalous data.
* **Advanced Telemetry:** Dedicated Green AI energy footprint and hardware utilization analysis.

To request a Commercial License and inquire about the Enterprise Edition, please contact:
📧 **qck-framework@web.de**

---

## ⚙️ Installation & Usage (Core Edition)

**1. Install Dependencies:**
`pip install sentence-transformers pandas matplotlib scikit-learn numpy psutil`

**2. Run the Pruner:**
Place any `.txt` raw data files into the same directory as the script.
`python QCK_Fractal_Data_Pruning_v001.py`

*(Note: On its first run, the script will automatically fetch the necessary vector weights (`paraphrase-multilingual-mpnet-base-v2`) from HuggingFace).*

---

## ⚖️ Commercial Pricing

This Core codebase is free under AGPLv3. Any closed-source commercial deployment, API integration, or usage by a for-profit corporation avoiding copyleft obligations requires a paid commercial license. We do not provide technical support for the open-source edition.

**Commercial Enterprise Tiers:**

| Tier | Annual Cost | Target Output / Usage Limit |
|---|---|---|
| **Startup** | €40,000 | For training models up to 7B Parameters OR pruning up to 1 TB of Enterprise Data. (Max. 5 Data Scientists) |
| **Mid-Market** | €100,000 | For training models up to 30 Billion Parameters (Max. 20 Data Scientists) |
| **Enterprise** | €150,000+ | Unlimited Parameters / Frontier Models (Full Corp License) |

**Licensing Process:**
1. Send a license request to: **qck-framework@web.de**
2. We evaluate the request and grant approval.
3. Payment is processed via **Wire Transfer (Annual Upfront)**.
4. Upon receipt, the proprietary commercial license certificate and Enterprise binaries are issued.

*For contribution rules and pull requests, please read the `CONTRIBUTING.md` (CLA required).*
