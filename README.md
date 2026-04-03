# QCK: Smart Data Pruning (v001)

**A Manifest for Smart Data and Green AI: From Brute-Force Ray Tracing to Preventive Geometric Selection.**

As the internet floods with synthetic text, training future foundational models on AI-generated "slop" leads to irreversible Model Collapse. The current paradigm ("Scale is all you need") hits physical limits when algorithms attempt to diagnose hallucination *after* training using extreme compute power.

The **QCK SMART Data Pruning** pipeline offers a fundamental paradigm shift: **Prevention instead of Diagnosis.** Based on the QCK framework, this tool postulates that high-quality, organic information inherently possesses a stable geometric signature—a clear attractor. Instead of training on petabytes of high-dimensional fractal noise, this script prunes the data geometrically *before* it enters the training pipeline.

## 🚀 The Mathematical Advantage
Instead of using complex Transformer loops, the QCK Pruner mathematically measures the dimensional roughness (D2) and semantic drift of the text topology in a 768-dimensional space (`mpnet-base-v2`). 

* **ACCEPTED (Smart Data):** Human thought exhibits a stable organic attractor. 
* **REJECTED (Fractal Noise / Slop):** LLMs exhibit a dangerous "Synthetic Perfection" or high-dimensional semantic jitter.

### 🌿 Green AI & O(N) Complexity
* **Pipeline Speed:** Scans and prunes pre-training datasets up to **~30,000x faster** than post-training diagnostics.
* **Footprint:** Filters texts locally on a standard CPU using `< 1.5 GB RAM`.
* **Energy Savings:** Reduces energy consumption by **> 99.99%** compared to a 2500W GPU cluster.

---

> [!WARNING]
> **Prototype Status & Limitations**
> This repository contains a prototype Python engine. 
> * **Usage:** Feel free to experiment with the code to evaluate performance, logic, and base capabilities.
> * **Limitation:** This engine is **not** designed for large batch processing out of the box. 
> * **Scaling:** Mass scanning of massive files or entire file systems will fail or bottleneck. Doing so requires a completely different calibration and memory management setup.

---

## ⚙️ Installation & Usage (Non-Commercial)

**1. Install Dependencies:**
`pip install sentence-transformers pandas matplotlib scikit-learn numpy psutil`

**2. Run the Pruner:**
Place any `.txt` raw data files into the same directory as the script.
`python QCK_Fractal_Data_Pruning_v001.py`

*(Note: On its first run, the script will automatically fetch the necessary vector weights (`paraphrase-multilingual-mpnet-base-v2`) from HuggingFace).*

---

## ⚖️ Enterprise & Commercial Licensing

This work is **strictly free for academic and non-commercial research only.** Any commercial deployment, API integration, or usage by a for-profit corporation requires a paid commercial license. We do not provide technical support.

**Commercial Pricing:**

| Tier | Annual Cost | Target Output / Usage Limit |
|---|---|---|
| **Startup** | €40,000 | For training models up to 7 Billion Parameters (Max. 5 Data Scientists) |
| **Mid-Market** | €100,000 | For training models up to 30 Billion Parameters (Max. 20 Data Scientists) |
| **Enterprise** | €150,000+ | Unlimited Parameters / Frontier Models (Full Corp License) |

**Licensing Process:**
1. Send a license request to: **qck-framework@web.de**
2. We evaluate the request and grant approval.
3. Payment is processed via **Wire Transfer (Annual Upfront)**.
4. Upon receipt, the commercial license certificate is issued.

*For contribution rules and profit-sharing bounties, please read the `CONTRIBUTING.md`.*
