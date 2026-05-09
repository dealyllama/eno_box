# Research Plan: FM Synthesis

**Goal:** Identify lightweight, efficient Frequency Modulation (FM) synthesis techniques and DSP implementation patterns that can be prototyped in Python and subsequently ported to an ESP32/C++ environment.

---

#### **[CHECKPOINT 1] Phase 1: FM Algorithm & DSP Library Survey**
**Objective:** Map the mathematical foundations of FM synthesis and identify Python tools for waveform generation.

*   **Technique Investigation (DSP Focus):**
    *   **FM Fundamentals:** Research Operator-based synthesis (4-op, 6-op), modulation indices, and sideband calculation.
    *   **Waveform Generation:** Evaluate efficient methods for generating sinusoids, which are the basis for FM (e.g., phase modulation vs. pure frequency modulation).
    *   **Complexity Analysis:** Identify methods that can run with minimal CPU overhead (e.g., Wavetable lookup vs. real-time trigonometric calculation).
*   **Library Identification & Audit:**
    *   Analyze Python libraries (`NumPy`, `SciPy.signal`, `mido`) for their ability to handle high-speed DSP buffers.
    
    *   **Critical Metric:** Evaluate the "Computational Cost" of the synthesis loop—can the logic be translated to fixed-point arithmetic for ESP32?
*   **Deliverable:** A list of "Port-Ready" FM algorithms and Python DSP building blocks.

---

#### **[CHECKPOINT 2] Phase 2: DSP Implementation & Portability Analysis**
**Objective:** Analyze existing C++ and Python DSP implementations to understand the bridge between high-level prototyping and low-level execution.

*   **Repository Mining:** Search for Python-based FM synthesis engines and small-scale C++ DSP snippets.
*   **Structural Analysis:** 
    *   Examine the separation of the **FM Engine** (the oscillator/operator logic) from the **Audio Buffer Management** (the output stream).
    *   Analyze how "look-up tables" (LUTs) are used in C++ to replace expensive `sin()` calls and how to prototype this in Python.
*   **Algorithmic Translation Potential:** 
    *   Assess the difficulty of moving from `NumPy` array-based oscillations to C++ pointer-based buffer manipulation.
    *   Identify C++ DSP frameworks or libraries (e.g., `Faust`, `JUCE`, or `Espressif ESP-DSP`) that can host the ported algorithms.
*   **Deliverable:** A technical blueprint for a cross-platform FM synthesis engine.

---

#### **[CHECKPOINT 3] Phase 3: Synthesis & Final Report**
**Objective:** Produce the final research document for architectural decision-making.

*   **Synthesis:** Create a comparison matrix of FM approaches: **Operator Count | Modulator Complexity | Memory Usage (LUT size) | C++ Portability**.
*   **Documentation standards:** Use `research/system-instructions.md` and `research/citation-standards.md` (APA 7th).
*   **Deliverable:** `research/fm_synthesis_research_report.md` containing:
    *   Executive Summary.
    *   The "Efficient FM" Algorithm Matrix.
    *   Implementation Strategies (Python $\rightarrow$ C++).
    *   References (APA).
