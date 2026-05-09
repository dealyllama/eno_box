# Plan: Python-to-C++ Coding Convention Research

#generated-content-danger-will-robinson

## TL;DR: Research Abstract
This plan outlines a research strategy to establish coding conventions for Project Eno-Box that bridge the gap between high-level Python prototyping and low-level, resource-constrained C++ (ESP3/C++) implementation. The focus is on identifying **Data-Oriented Design (DOD)** and **Array-based** patterns that translate seamlessly between NumPy-style vectorized logic and C++ buffer-based signal processing.

---

## 1. Goal
Define a set of coding conventions (paradigm, data structures, and algorithmic patterns) that ensure Python prototypes are "translatable-by-design" to highly efficient, fixed-point C++ code for the ESP32.

## 2. Current Context & Assumptions
*   **Current State:** FM synthesis research is complete; the project is moving into structural definition.
*   **Target Hardware:** ESP32/C++ (memory-constrained, preference for fixed-point, performance-critical).
*   **The "Gap":** Python encourages high-level abstractions (OOP, dynamic typing, high-level object hierarchies) which are often "illegal" or prohibitively expensive in embedded C++.
*   **Assumption:** A **Data-Oriented approach** (treating signals as contiguous buffers rather than collections of "Oscillator" objects) is the most portable path.

## 3. Proposed Approach: The "Bridge" Strategy
I will research and document conventions centered around **Data-Oriented Design (DOD)**. Instead of researching general Python/C++ differences, I will focus specifically on the **intersection of NumPy logic and ESP-DSP implementation.**

### Research Dimensions:
1.  **Paradigm: OOP vs. Procedural/Functional**
    *   Investigate the overhead of C++ polymorphism (vtable look/virtual functions) vs. the simplicity of pure functions acting on buffers.
    *   Evaluate how "Stateless" functions (Functional) in Python can be mapped to "Buffer-in/Buffer-out" functions in C++.
2.  **Data Structures: Objects vs. Tensors/Arrays**
    *   Compare "Object-per-Oscillator" (OOP) vs. "Parallel Arrays for Phase/Freq/Amp" (DOD).
    *   Research how `numpy.ndarray` structures map directly to `float*` or `int16_t*` buffers in C++.
3.  **Algorithmic Translation: Vectorization vs. Iteration**
    *   Research how high-level NumPy vector operations (e.g., `A * sin(B)`) can be documented as "loop-friendly" instructions that translate to `esp-dsp` or `microdsp` primitive loops (e.g., `dsps_dotprod_s16`).

## 4. Step-by-Step Plan

### Step 1: Pattern Identification (Literature Review)
*   Review `esp-dsp` and `microdsp` source code/examples to identify the "Native C/C++ Pattern" (e.g., how they handle buffer ownership and iteration).
*   Review `NumPy` and `SciPy` implementation "idioms" (e.s., broadcasting, slicing, element-wise operations).

### Step 2: The "Convention Manifesto" Creation
*   Document a set of **"Prohibited Python-isms"** (e.g., "Avoid nested dictionaries for DSP parameters; use flat arrays/structs").
*   Document **"Approved Python Patterns"** (e.g., "Use NumPy slicing to represent buffer windows; this maps to pointer arithmetic in C++").

### Step 3: Validation via Prototype
*   Take a simple FM synthesis snippet from our existing research and rewrite it following the new "Bridge Convention."
*   Check if the Python code looks like "C code written in Python."

## 5. Files Likely to Change
*   `research/coding_conventions_report.md` (The final outcome/manifesto).
*   `TODO.md` (Updating task status).
*   `research/logs/convention_research_accumulation.md` (The research log).

## 6. Risks & Open Questions
*   **Risk:** If we go *too* low-level in Python, we lose the "Ease of Use" that makes Python great for prototyping.
*   **Open Question:** To what extent should we allow "high-level" Python orchestration (orchestrating the DSPs) vs. the "DSP-core" logic?
