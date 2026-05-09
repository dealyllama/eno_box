# FM Synthesis Research Report: Architecture, Complexity, and Implementation

#generated-content-danger-will-robinson

## TL;DR: Research Abstract
This report synthesizes research regarding Frequency Modulation (FM) and Phase Modulation (PM) synthesis for implementation on resource-constrained hardware (specifically ESP32/C++). Key findings indicate that **Phase Modulation (PM)** is preferred for embedded systems due to its stable modulation index and avoidance of DC frequency shifts. For waveform generation, **Wavetable Lookup** with interpolation (linear or quadratic) offers a superior balance of performance and harmonic richness compared to real-time trigonometric calculations, provided memory allows. For the target environment, the **`esp-dsp`** library is the primary recommendation for hardware-accelerated 16-bit operations, supplemented by **`microdsp`** or **`dsplib`** for portable, lightweight C++ abstractions.

---

## 1. Synthesis Architecture & Complexity

### 1.1 FM vs. PM Implementation
A critical distinction exists between Frequency Modulation (FM) and Phase Modulation (PM) in digital implementations:
- **FM (Frequency Modulation):** The modulator signal is added to the frequency (phase increment) before integration. High-frequency modulation can cause frequency shifts and instability in the spectrum.
- **PM (Phase Modulation):** The modulator signal is added directly to the phase. This is mathematically equivalent to FM if the modulator is integrated, but PM is significantly more stable for embedded systems because the modulation index ($\beta$) remains constant regardless of the modulator'frequency.
- **Recommendation:** Implement **Phase Modulation (PM)**. It simplifies the calculation of the modulation index and prevents the "DC frequency shift" problem common in pure FM.

### 1.2 Operator Configuration (4-op vs. 6-op)
- **4-Operator (e.g., DX7-style):** Focuses on two 2-operator pairs. Provides high efficiency and is sufficient for many classic FM timbres.
- **6-Operator (e.g., DX7/OPL3-style):** Allows for more complex algorithms (e._g_, feedback loops, more complex routing). 
- **Decision Factor:** The complexity of the "Algorithm" (how operators are routed) heavily impacts the CPU overhead. For an ESP32 target, a fixed set of 4-operator or 6-operator algorithms is recommended to minimize routing logic complexity.

---

## 2. Waveform Generation: Wavetable vs. Trigonometry

### 2.1 Computational Trade-offs
| Method | Pros | Cons |
| :--- | :--- | :--- |
| **Real-time Trigonometry** | No memory footprint for tables; high precision. | Extremely high CPU cost on microcontrollers; lacks efficiency for complex waveforms. |
| **Wavetable Lookup** | Very fast; low CPU overhead (single memory read). | Requires RAM/Flash for tables; introduces aliasing/error without interpolation. |
_
**Key Finding:** **Wavetable Lookup with Linear or Quadratic Interpolation** is the optimal choice. While non-interpolated tables are fastest, the error (noise) is significant. Linear/Quadratic interpolation provides a "sweet spot" for SNR vs. CPU usage on hardware like the ESP32.

### 2.2 Fixed-Point Considerations
To maximize the performance of the ESP32, algorithms should utilize **fixed-point arithmetic (16-bit/32-bit)** where possible, leveraging the `esp-dsp` library's optimized functions. Avoiding floating-point operations in the inner synthesis loop is critical for maintaining high polyphony.

---

## 3. Library Audit & Recommendations

For an ESP32/C++ development workflow, the following libraries were evaluated:

| Library | Best Use Case | Pros | Cons |
| :--- | :--- | :--- | :--- |
| **`esp-dsp`** | **Primary Engine** | Official Espressif library; highly optimized for ESP32/S3; supports 16-bit/float; hardware-accelerated (FFT, FIR, Dot Product). | Target-specific (ESP32 only). |
| **`microdsp`** | Lightweight DSP | Extremely low footprint; streaming/sliding window mode; perfect for resource-constrained microcontrollers. | Limited feature set compared to high-end libraries. |
| **`dsplib`** | High-level C++ | Modern C++17; MATLAB-like syntax; great for prototyping complex algorithms in C++. | Higher abstraction overhead; C++ dependency. |
| **`NanoSNAP`** | Portability | C++11; highly portable; great for cross-platform testing (Desktop to ESP3_S3). | Focused more on signal processing than synthesis-specifics. |
| **`KFR`**| High Performance | Extremely fast; modern features; robust. | Heavy dependency; likely too heavy for the core ESP32 engine. |

## 4. Final Implementation Roadmap
1.  **Prototype (Python):** Use `NumPy`/`SciPy` to validate the FM/PM algorithms and the 4-op/6-op routing logic.
2.  **Porting (C++):** Implement the core Phase Modulator using `esp-dsp` for 16-bit fixed-point math.
3.  **Optimizing:** Use **Wavetable Lookup** with **Linear Interpolation** for the primary sine/waveform generation to minimize CPU cycles.
4.  **Expansion:** Once the core engine is stable, expand the algorithm set to include 6-operator configurations.
