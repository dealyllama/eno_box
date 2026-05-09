# Research Plan: Stochastic Music Generation

**Goal:** Identify lightweight, portable stochastic music generation techniques and libraries that can inform a Python prototype while remaining feasible for a future ESP32/C++ port.

---

#### **[CHECKPOINT 1] Phase 1: Algorithmic & Library Landscape Survey**
**Objective:** Map the "state of the art" for stochastic processes, specifically filtering for computational efficiency and C++ portability.

*   **Technique Investigation (Portability Focus):**
    *   **Low-Complexity Algorithms:** Evaluate Markov Chains, L-Systems, and simple Brownian motion (high portability, low RAM).
    *   **Medium-Complexity Algorithms:** Evaluate Gaussian processes and rule-based grammars (moderate complexity).
    *   **High-Complexity/Excluded:** Evaluate Transformers/ML-based models (flag as "Research Only" due to ESP32 infeasibility).
*   **Library Identification & Audit:**
    *   Analyze Python libraries (`MusPy`, `mido`, `scipy.stats`) for dependency overhead.
    *   **Critical Metric:** Evaluate the "Logic vs. Weight" ratio—is the library a wrapper around a simple algorithm, or does it require a heavy runtime?
*   **Deliverable:** A list of "Port-Ready" vs. "Prototype-Only" algorithms and libraries.

---

#### **[CHECKPOINT 2] Phase 2: Implementation & Case Study Analysis**
**Objective:** Analyze real-world implementations to understand structural patterns and algorithmic translation.

*   **Repository Mining:** Search for Python implementations of the "Port-Ready" algorithms identified in Phase 1.
*   **Structural Analysis:** Analyze how successful projects separate the **Stochastic Engine** (the logic) from the **Audio/MIDI Driver** (the output).
*   **Algorithmic Translation Potential:** 
    *   Assess the ease of translating Python logic (e.g., `numpy` operations) into C++ (e.g., manual array manipulation or `Eigen` library).
    *   Identify existing C++ libraries for generative music (e.g., `eeld`, etc.) that the Python logic could mimic.
*   **Deliverable:** A structural blueprint for a decoupled generative engine.

---

#### **[CHECKPOINT 3] Phase 3: Synthesis & Final Report**
**Objective:** Produce the final research document for architectural decision-making.

*   **Synthesis:** Create a comparison matrix of algorithms: **Complexity | RAM Footprint | C++ Portability | Implementation Difficulty**.
*   **Documentation standards:** Use `research/system-instructions.md` and `research/citation-standards.md` (APA 7th).
*   **Deliverable:** `research/stochastic_music_generation_report.md` containing:
    *   Executive Summary.
    *   The "Portability-First" Algorithm Matrix.
    *   Case Studies.
    *   References (APA).
