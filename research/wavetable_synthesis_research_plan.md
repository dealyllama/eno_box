# Research Plan: Wavetable Synthesis

**Goal:** Identify efficient, memory-optimized wavetable synthesis techniques and implementation patterns that can be prototyped in Python and subsequently ported to an ESP32/C++ environment.

---

#### **[CHECKPOINT 1] Phase 1: Wavetable Algorithm & Library Survey**
**Objective:** Map the mathematical foundations of wavetable synthesis and identify Python tools for waveform generation and interpolation.

*   **Technique Investigation (Memory & CPU Focus):**
    *   **Waveform Generation:** Research methods for generating base waveforms (Sine, Saw, Square, Triangle) for table population.
    *   **Interpolation Algorithms:** Evaluate the computational cost and audio quality of different interpolation methods (Linear, Hermite, Cubic) as they apply to table reading.
    *   **Compression/Encoding:** Investible research into efficient waveform storage (e.g., using Fourier coefficients or mathematical approximations) to minimize RAM usage on ESP32.
*   **Library Identification & Audit:**
    *   Analyze Python libraries (`NumPy`, `SciPy`, `mido`) for their ability to handle buffer-based waveform-table manipulation.
    *   **Critical Metric:** Evaluate the "Memory vs. Fidelity" trade-off—how much RAM is required for high-resolution tables versus the CPU cost of higher-order interpolation.
*   **Deliverable:** A list of "Port-Ready" wavetable strategies and Python-compatible waveform generation tools.

---

#### **[CHECKPOINT 2] Phase 2: Implementation & Portability Analysis**
**Objective:** Analyze existing C++ and Python wavetable implementations to understand the bridge between high-level prototyping and low-level execution.

*   **Repository Mining:** Search for Python-based wavetable oscillators and small-scale C++ DSP snippets.
*   **Structural Analysis:** 
    *   Exprect the separation of the **Wavetable Manager** (table loading/generation) from the **Oscillator Engine** (the read/interpolate/output loop).
    *   Analyze how "Lookup Tables" (LUTs) are managed in memory and how to prototype this in Python using `memoryview` or `NumPy` arrays.
*   **Algorithmic Translation Potential:** 
    *   Assess the difficulty of moving from Python/NumPy array-based indexing to C++ pointer-based buffer traversal.
    *   Identify C++ DSP frameworks or libraries (e.g., `Arm CMSIS-DSP` for ESP32) that can optimize interpolation math.
*   **Deliverable:** A technical blueprint for a cross-platform wavetable synthesis engine.

---

#### **[CHECKPOINT 3] Phase 3: Synthesis & Final Report**
**Objective:** Produce the final research document for architectural decision-making.

*   **Synthesis:** Create a comparison matrix of wavetable approaches: **Interpolation Type | RAM Footprint (Table Size) | CPU Cost | C++ Portability**.
*   **Documentation standards:** Use `research/system-instructions.md` and `research/citation-standards.md` (APA 7th).
*   **Deliverable:** `research/wavetable_synthesis_research_report.md` containing:
    *   Executive Summary.
    *   The "Memory-Efficient" Wavetable Matrix.
    *   Implementation Strategies (Python $\rightarrow$ C++).
    *   References (APA).
