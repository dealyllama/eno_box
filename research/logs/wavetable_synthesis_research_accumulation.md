

## Research Session: 2026-05-09 11:38:21
**Focus:** Waveform Generation & Compression Techniques

### Raw Findings
#### Topic: Efficient waveform generation algorithms (Sine, Saw, Square, Triangle) for wavetable synthesis
- **Table Lookup Oscillator | RingBuffer** (http://ringbuffer.org/sound_synthesis_introduction/Processed_Recording/table_lookup_oscillator/)
  Table-lookup oscillators are essential in digital signal processing and audio synthesis. They are widely used to generate periodic waveforms such as sine, square, triangle, and sawtooth waves. Beyond that, the method can be used to sythesize arbitrary waveforms, which is its key strength.
[...]
- Flexibility: The same framework can generate different waveforms - and thus any timbre - by using different tables.
- Efficiency: By precomputing a waveform and storing it in a lookup table, the computation during runtime is minimal.
[...]
noise. For many applications, a well-designed table is accurate enough.
[...]
- Memory Usage: This is usually not a problem, but the more precise a
[...]
A lookup table is a sampled version of an arbitray continuous waveform. For basic periodic waveforms, the table holds exactly one cycle, sometimes only half or the quarter of a cycle, making use of symmetries. Table sizes of 256-4096 samples are common in sound synthesis applications.
[...]
This method in 3 steps ensures smooth waveform generation with high accuracy and efficiency, suitable for real-time audio or signal processing applications.
[...]
- This smooths out the waveform, especially for higher frequencies or low-resolution tables.
- When the phasor lands between two indices in the table, linear interpolation is used to compute an in-between value.
- The fractional part of the phase is used for interpolation between the current and next index.
- The integer part of the phase determines the index in the table.
[...]
## Efficiency¶
[...]
### Efficiency of Lookup Table Oscillators¶
[...]
Generating a waveform involves:
[...]
- Simple arithmetic: Updating the phase index and performing modulo operations.
- Memory access: Fetching a value from an array.
[...]
- These calculations require multiple multiplications, additions, and sometimes divisions, making them computationally expensive.
- Trigonometric calculations: Implementations use Taylor series, CORDIC algorithms, or similar methods.
[...]
- `sin()` Function: ( O(n) ), where ( n ) depends on the precision required.
- Lookup Table Oscillator: ( O(1) ) per sample (array lookup + basic arithmetic).
[...]
- This results in 10–100x improvement in speed for a lookup table oscillator.
- A lookup table operation (memory access + arithmetic) might take 1–5 nanoseconds [2][3].
- A single`sin()` computation can take 50–150 nanoseconds [1].
[...]
- Total computation time: ~0.1 ms.
- Perform ( 44,100 ) memory accesses and phase updates, each taking ~1–5 ns.
[...]
The Signal-to-Noise Ratio (SNR) and quantization noise for a table lookup sine oscillator depend on the table size N, which is the number of samples per cycle used in the lookup table. A larger table size improves the accuracy of the sine wave representation and thus reduces the quantization error (noise) and increases the SNR.
[...]
The Signal-to-Noise Ratio (SNR) for a table lookup sine oscillator is influenced by the size of the lookup table and the interpolation method used. Larger tables result in more accurate sine wave approximations, while interpolation techniques (like linear or cubic interpolation) reduce quantization noise, improving the overall SNR. The quantization in the time domain has the same effect as a quantization in the amplitude range.
[...]
#### Effect of Interpolation:¶
[...]
- For even higher precision, cubic interpolation can be used, but at a greater computational expense.
- Linear interpolation provides a balance between computational cost and accuracy.
- Without interpolation, the output waveform can sound rough or distorted, especially when the table resolution is low.
- Even with interpolation, lookup tables remain faster than direct computation.
- Lookup tables can use interpolation (e.g., linear or cubic) to improve accuracy, adding some computational overhead.
[...]
Linear interpolation reduces the quantization noise compared to using no interpolation, improving the SNR by approximately 3 dB.
- **Software Synthesis** (https://basicsynth.com/index.php?page=wvtables)
  A general principle of computer programming is that we can trade memory for computational speed. If we store a waveform in a table, we can then index into the table to get the values rather than repeatedly calculating them. For periodic waveforms we only have to calculate one cycle of the waveform, an even greater savings. We can produce a continuous sound if we loop back to the beginning of the table when we reach the end. In other words, we increment the table index modulo the table length.
[...]
Given that the table contains one complete cycle of the waveform, the table length represents 2π radians. Thus, with a fixed table length, we calculate the increment value for the index by dividing the table length by the samples per period. This will give us an index value that will cause the code to skip through the table at different rates for different frequencies. We must allocate a table and fill it with a sin wave as before, but now we can keep the table in memory at all times. Generating the sound is reduced to calculating an index and retrieving the sample values from the table.
[...]
The wavetable does not have to be filled with a sine wave. We can sum sine waves together when we initialize the table, or even load the waveform from a file to playback a recorded or pre-computed waveform. We can also change between wavetables during sound generation to produce "morphing" of the waveform. Furthermore, the wavetable does not have to be limited to one cycle of the waveform. If we have a waveform that varies over some regular number of periods, we can still build a table for that waveform if we include multiple periods. However, when more than one period is included, the increment must be multiplied by the number of periods to produce the correct frequency.
[...]
Using this type of interpolation adds some computation time, but is still much faster than repeatedly recalculating the waveform using the*sin*function.However, there is a possibility to get nearly the same results without interpolation. The amount of error in the sample value will depend in large part on the table length. For short tables, we have a small set of index values to select from and will be more likely to introduce an error than with a longer table.
[...]
The table index value has a fixed number of bits available to represent the integer and fractional parts of the index. With longer tables, the index varies over a larger range and thus more bits are contained in the integer portion of the index. For example, with a table of 256 values, only 8 bits of the index are used for the integer portion. For a table of 512 values, 9 bits are used, etc. With each doubling of the table length we gain one bit of precision in the index and are discarding one less bit of precision to obtain an integer index. This results in a smaller variation between the lookup value and the directly calculated value. In effect, lengthening the table is much like performing interpolation on a shorter table once rather than for each sample. Lengthening the table also means we have fewer bits in the fractional portion of the index and the quality of the interpolation diminishes. Thus, for shorter tables (4k or less) it is best to use interpolation, but for longer tables it is sufficient to simpy round-off the index. A table of 16K size generally works very well. Note that to make best use of the index range, table length should be a power of two.
- **Niels Moseley's bloggy bits** (https://namoseley.wordpress.com/2015/07/26/sincos-generation-using-table-lookup-and-iterpolation/)
  In the past, I’ve written several blog posts on calculating sin(x) and cos(x) using polynomials. These methods can be very accurate but require many multiplications and additions. For usage in hand-held calculators, where a few evaluations per second are enough, these algorithms are fine. However, in high-speed signal processing and generation applications, such as software-defined radio or test equipment, a less computationally intensive method is required.
[...]
A simple way to generate waveforms is to read the contents of a table, which resides in memory, at a constant speed. Usually, a single cycle of the desired waveform is stored in the table. When the table hasNnumber of entries and the table entries are read at a speed ofF_sentries per second, the frequency of the generated waveform will beF=F_s/N Hz.
[...]
# Algorithm #1: Table lookup
[...]
To generate a sine (or cosine) wave, we must simply fill the table with sin(x). The table entries(k=0 \ldots N-1)are thus:table[k] = \sin \left (2 \cdot \pi \cdot \frac{k}{N} \right ).
[...]
reached. This
[...]
. This way,
[...]
# Algorithm #2: Table lookup and interpolation
[...]
We can easily get better results without increasing the number of table entries significantly by interpolating between the table values; we have the fractional part! The following algorithm uses linear interpolation:
[...]
Spectrum of a sine wave generated by the table-lookup with interpolationmethodClearly, the linear interpolation increases the spectral purity significantly! There are fewer spurious responses and the highest one is better than -90 dBc. For software radio applications this good enough.
[...]
# Algorithm #3: Table lookup and circular interpolation
[...]
There are certain applications where -90 dBc of spectral purity isn’t enough, for instance, in test equipment, very high purity digital down-converters are needed for precise measurements, e.g. to reduce noise folding. To increase the spectral purity, we could increase the table size, or we could choose a better interpolation method.
[...]
One way of getting a better interpolation method would be to use following identity:
[...]
\sin(A+B) = \sin(A) \cdot \cos(B) + \cos(A) \cdot sin(B)
[...]
If we choose A to be derived from the upper 8 bits (integer part) of the phase accumulator, we can lookup\sin(A)and\cos(A)using our table and these lookups will be*exact*. Note that\cos(A) = \sin(A + \frac{\pi}{2}), so\cos(A)is simply found 64 entries further in the table. Then, B is the fractional angle, which is represented by the lower 16 bits of the phase accumulator. As the fractional angle is always somewhere between the table entries, we cannot use our lookup table. We could use a second lookup table or we can use the well-known approximations of\sin(x)and\cos(x)for small angles:
[...]
\sin(x) \approx xand\cos(x) \approx 1-\frac{x^2}{2}.
[...]
Spectrum of a sine wave generated by the table-lookup with circular interpolationThe circular interpolation gives an improvement over linear interpolation at the cost of an additional table lookup and a few multiplications and additions. The spurious responses are now found at around -128 dBc, which is 38 dB better.
[...]
Further experiments show that increasing the table size to 512 entries produces spurious responses that are below -147 dBc. A table size of 128 entries gives responses below -110 dBc.
[...]
The table-lookup and interpolate method of signal generation is a powerful way to generate high-purity signals without resorting to complex approximations.With simple linear interpolation, a 256-entry table can produce sine and cosine waveforms with spurious responses that are around 90 dB below the carrier. At the expense of a few additional operations, spurious responses can be as low as -128 dBc by using circular interpolation.
[...]
The presented algorithms are also well suited to generate quadrature signals as both sine and cosine waveforms can be generated simultaneously.
- **Wavetable Synthesis Algorithm Explained | WolfSound** (https://thewolfsound.com/sound-synthesis/wavetable-synthesis-algorithm/)
  - how is wavetable synthesis related to other synthesis methods.
- what are pros and cons of wavetable synthesis, and
- step-by-step wavetable synthesis algorithm (also known as fixed-waveform synthesis [7]),
- how to generate sound using wave tables,
[...]
## A Need for a Fast and Efficient Synthesis Method
[...]
In the early days of digital sound synthesis, sound was synthesised using specialized digital signal processing hardware. Later on, the community started using software for the same purposes but the underlying principles and algorithms remained the same. To obtain real-time performance capabilities with that technology, there was a great need to generate sound efficiently in terms of memory and processing speed. Thus, the wavetable technique was convceived: it is both fast and memory-inexpensive.
[...]
A gesture provides control information. In the case of pressing a key on a MIDI keyboard, control information would incorporate information on which key was pressed and how fast was it pressed (velocity of a keystroke). We can change the note number information into frequency ff and the velocity information into amplitude AA. This information is sufficient to generate sound using most of the popular synthesis algorithms.
[...]
## Sine Generator
[...]
Let’s imagine that given frequency and amplitude information we want to generate a sine wave. The general formula of a sine waveform is
[...]
s(t)=Asin⁡(2πft+ϕ),(1)s(t) = A \sin (2 \pi f t + \phi), \quad (1)
[...]
`sin()` calls use the Taylor expansion of the sine function [1]
[...]
Above expansion is infinite, so on real-world hardware, it needs to be truncated at some point (after obtaining sufficient accuracy). Its advantage is, that it uses operations realizable in hardware (multiplication, division, addition, subtraction). Its disadvantage is that it involves a lot of these operations. If we need to produce 44100 samples per second and want to play a few hundred sines simultaneously (what is typical of additive synthesis), we need to be able to compute the sin⁡\sin function more efficiently than with Taylor expansion.
[...]
## A Wave Table
[...]
A wave table is an array in memory in which we store a fragment of a waveform. A waveform is a plot of a signal over time. Thus, one period of a sine wave stored in memory looks as follows:
[...]
The above wave table uses 64 samples to store one period of the sine wave. These values can be calculated using the Taylor expansion because we compute them only once and store them in memory.
[...]
sin⁡\sin period is exactly 2π2 \pi. The period of a wave table is its length, let’s denote it by LL. For each sample index k∈{0,…,L−1}k \in \{0, \dots, L-1\} in the wave table, there exists a corresponding argument θ∈[0,2π)\theta \in [0, 2\pi) of the sine function.
[...]
kL=θ2π.(5)\frac{k}{L} = \frac{\theta}{2 \pi}. \quad (5)
[...]
## Computing a Waveform Value from the Wave Table
[...]
Equation 5 holds for θ∈[0,2π)\theta \in [0, 2\pi). If we want to calculate the values of arbitrary x∈Rx \in \mathbb{R}, we need to remove the multiplicity of 2π2 \pi contained in xx to bring it to the [0,2π)[0, 2\pi) range. In other words, if
[...]
x=2πl+ϕx,ϕx∈[0,2π),(6)x = 2\pi l + \phi_x, \quad \phi_x \in [0, 2\pi), \quad (6)
[...]
then we want to find ϕx\phi_x. In software, it can be done by subtracting or adding 2π2 \pi to xx until we obtain a value in the desired range. Alternatively, we can use a function called`fmod()`, which allows us to obtain the remainder of a floating-point division.
[...]
We can subsequently compute the corresponding index in the wave table from the proportion in Equation 5.
[...]
k=ϕxL2π.(7)k = \frac{\phi_x L}{2\pi}. \quad (7)
[...]
Now`waveTable[k]`
[...]
return the value of sin⁡(x)\sin(x),
[...]
In most cases, kk computed in Equation 7 won’t be an integer. It will rather be a floating-point number between some two integers denoting the wave table indices, i.e., i<=k
- **Quarter sine wave look up table synth with various oscillators in Nim · GitHub** (https://gist.github.com/ingoogni/b115408fb20d58cffa4d09c343a0a057)
  Quarter sine wave look up table synth with various oscillators in Nim
[...]
| import math |
| --- |
| const |
| SampleRate {.intdefine.} = 44100 |
| SRate* = SampleRate.float |
| LutSize = 256 # Quarter sine wave |
| Lut = static: |
| var arr: array[LutSize, float] |
| for i in 0.. = 2.0 * PI: |
| gen.phase -= 2.0 * PI |
| template advanceModPhase*()= |
| gen.phase += modulatedIncrement |
| if gen.phase >= 2.0 * PI: |
| gen.phase -= 2.0 * PI |
[...]
| proc nextSample*(gen: var WaveGenerator): float = |
| # Generate sine wave using quarter LUT |
| result = gen.lookupSine(gen.phase) |
| advancePhase() |
[...]
| proc nextSawtooth*(gen: var WaveGenerator): float = |
| # "Sawtooth" by stepping through LUT linearly (ignoring symmetry) |
| advancePhase() |
| let |
| normalizedPhase = gen.phase / (2.0 * PI) |
| index = int(normalizedPhase * LutSize.float) |
| clampedIndex = min(index, LutSize - 1) |
| return Lut[clampedIndex] * 2.0 - 1.0 # Center around 0 |
[...]
| proc nextTriangle*(gen: var WaveGenerator): float = |
| # Triangle wave using bidirectional LUT traversal |
| advancePhase() |
| let normalizedPhase = gen.phase / (2.0 * PI) |
| if normalizedPhase < 0.5: |
| # Forward through LUT |
| let |
| index = int(normalizedPhase * 2.0 * LutSize.float) |
| clampedIndex = min(index, LutSize - 1) |
| return Lut[clampedIndex] |
| else: |
| # Backward through LUT |
| let |
| index = int((1.0 - normalizedPhase) * 2.0 * LutSize.float) |
| clampedIndex = min(index, LutSize - 1) |
| return Lut[clampedIndex] |
[...]
| proc nextSquare*(gen: var WaveGenerator): float = |
| # Square wave using LUT threshold |
| advancePhase() |
| let |
| normalizedPhase = gen.phase / (2.0 * PI) |
| index = int(normalizedPhase * LutSize.float) |
| clampedIndex = min(index, LutSize - 1) |
| return if Lut[clampedIndex] > 0.5: 1.0 else: -1.0 |
[...]
| proc nextPhaseDistortion*(gen: var WaveGenerator, distAmount
[...]
float = 0.5): float = |
[...]
lookupSine(
[...]
seq[float]): float = |
[...]
advanceModPhase() |
[...]
| proc nextPDSaw*(gen: var WaveGenerator): float = |
| ## PD: Sawtooth-like, using variable rate by making first half of cycle fast, |
| ## second half slow |
| let |
| basePhaseIncrement = 2.0 * PI * gen.freq / gen.sampleRate |
| normalizedPhase = gen.phase / (2.0 * PI) |
| rateMultiplier = if normalizedPhase < 0.5: 3.0 else: 0.2 |
| modulatedIncrement = basePhaseIncrement * rateMultiplier |
| result = gen.lookupSine(gen.phase) |
| advanceModPhase() |
[...]
| proc nextPDSquare*(gen: var WaveGenerator): float = |
| ## PD: Square-like, using phase jumps |
| let |
| basePhaseIncrement = 2.0 * PI * gen.freq / gen.sampleRate |
| normalizedPhase = gen.phase / (2.0 * PI) |
| # Slow down near 0.25 and 0.75 |
| distanceFromPeak1 = abs(normalizedPhase - 0.25) |
| distanceFromPeak2 = abs(normalizedPhase - 0.75) |
| nearPeak = min(distanceFromPeak1, distanceFromPeak2) |
| # Speed up elsewhere |
| rateMultiplier = if nearPeak < 0.1: 0.1 else: 2.0 |
| modulatedIncrement = basePhaseIncrement * rateMultiplier |
| result = gen.lookupSine(gen.phase) |
| gen.phase += modulatedIncrement |
| if gen.phase >= 2.0 * PI: |
| gen.phase -= 2.0 * PI |
[...]
| when isMainModule: |
[...]
| var gen = initWaveGenerator() |
| gen.setFrequency(440.0) # A4 |
[...]
| #var tickerTape = Ticker(tick: 0) |
| var sample = 0.0 |
[...]
| proc tick*(): float = |
| sample = gen.nextSample() |
| #sample = gen.nextSawtooth() |
| #sample = gen.nextTriangle() |
| #sample = gen.nextSquare() |
| #sample = gen.nextPhaseDistortion(0.5) |
| #sample = gen.nextResonantSweep(0.8) |
| #let czSegments = @[2.0, 0.1, 0.5, 0.3, 1.5, 0.7] |
| #sample = gen.nextCZStyle(czSegments) |
| #sample = gen.nextPDSaw() |
| #sample = gen.nextPDSquare() |
| #sample = gen.nextRingMod(100.0) |
| #sample = gen.nextAM(110.0, 0.9) |
| #sample = gen.nextWaveshaped(3.0) |
| #sample = gen.nextDistorted(0) |
| #sample = gen.nextDistorted(1) |
| #sample = gen.nextDistorted(2) |
[...]
| #inc tickerTape.tick |
[...]
| return sample |

#### Topic: Compressed wavetable synthesis using Fourier coefficients for ESP32/embedded systems
- **danilogcrf2-oss/ESP32Synth** (https://github.com/danilogcrf2-oss/ESP32Synth)
  - **Wavetable Synthesis:** Use custom wavetables for unique, complex timbres. The engine supports both direct assignment and a memory-efficient registry system for tracker instruments.
[...]
### Wavetables: `setWavetable()`, `registerWavetable()`
[...]
Wavetables are arrays containing a single cycle of a custom waveform.
[...]
- `void setWavetable(uint8_t voice, const void* data, uint32_t size, BitDepth depth);`
[...]
* Assigns a wavetable directly to a single voice.
 * `data`: Pointer to the array of sample data.
 * `size`: The number of samples in the array.
 * `depth`: The bit depth of the data (`BITS_8` or `BITS_16`).
[...]
- `void registerWavetable(uint16_t id, const void* data, uint32_t size, BitDepth depth);`
[...]
* Registers a wavetable in a global lookup table with a unique `id` (0-999). This is the required method for using wavetables with **Tracker-Style Instruments**. It is highly memory-efficient as the wavetable is stored only once and referenced by its ID.
[...]
- `tools/Wavetables/WavetableMaker.py`: Generates wavetables from various sources.
- `tools/Samples/WavToEsp32SynthConverter.py`: Converts standard `.wav` files into `.h` header files compatible with the sampler engine.
[...]
## Advanced Usage: Wavetable Memory Management
[...]
A critical detail when working with wavetables is understanding the difference between the number of samples and the size of the data array in bytes. The `setWavetable()` and `registerWavetable()` functions expect the `size` parameter to be the **number of individual samples** in the waveform, not the byte size of the array.
[...]
When using C++'s `sizeof()` operator, you get the total size of the array in bytes. To get the correct number of samples, you must adjust this value based on the wavetable's bit depth.
[...]
### `BITS_16` (16-bit wavetables)
[...]
Each sample is an `int16_t`, which takes up 2 bytes. To
[...]
number of samples, you must **divide by 2**.
[...]
### `BITS_8` (8-bit wavetables)
[...]
### `BITS_4` (4-bit wavetables)
[...]
4-bit samples are packed, with **two samples stored in a single `uint8_t`**. To get the total number of samples, you must **multiply by 2**.
[...]
```cpp
// A 4-bit wavetable with 256 samples occupies 128 bytes.
const uint8_t my_wave_4bit[128] = { ... };
[...]
// Correct usage:
synth.setWavetable(0, my_wave_4bit, sizeof(my_wave_4bit) * 2, BITS_4);
```
[...]
`setWavetable
[...]
e `registerWavetable()` esperam que o
[...]
metro `size` seja o **número de amostras individuais** na forma de onda, e não o tamanho do array em bytes
[...]
### `BITS_16` (
[...]
16-bit)
[...]
`BITS_4` (
[...]
etables de 4-bit)
[...]
As amostras de 4
[...]
uint8_t`**. Para
[...]
- **Síntese Wavetable:** Use wavetables customizadas para timbres únicos e complexos. O motor suporta tanto atribuição direta quanto um sistema de registro eficiente em memória para instrumentos de tracker.
[...]
### Wavetables: `setWavetable()`, `registerWavetable()`
[...]
Wavetables são arrays contendo um único ciclo de uma forma de onda personalizada.
[...]
- `void setWavetable(uint8_t voice, const void* data, uint32_t size, BitDepth depth);`
[...]
* Atribui uma wavetable diretamente a uma única voz.
 * `data`: Ponteiro para o array de dados da amostra.
 * `size`: O número de amostras no array.
 * `depth`: A profundidade de bits dos dados (`BITS_8` ou `BITS_16`).
[...]
- `void registerWavetable(uint16_t id, const void* data, uint32_t size, BitDepth depth);`
[...]
* Registra uma wavetable em uma tabela de pesquisa global com um `id` único (0-999). Este é o método necessário para usar wavetables com **Instrumentos Estilo Tracker**. É altamente eficiente em memória, pois a wavetable é armazenada apenas uma vez e referenciada pelo seu ID.
[...]
- `tools/Wavetables/WavetableMaker.py`: Gera wavetables a partir de várias fontes.
- `tools/Samples/WavToEsp32SynthConverter.py`: Converte arquivos `.wav` padrão em arquivos de cabeçalho `.h` compatíveis com o motor de sampler.
- **Additive synthesis on a CPU and memory constrained ARM platform - tsoniq** (https://tsoniq.net/blog/2023/viper-additive-synthesis/)
  plugins like fusion and phase7 use variations of the PolyBLEP family of algorithms.
[...]
simple waveforms such as saw or square waves, but are difficult and computationally
[...]
with more complex waveforms such as are produced by wavetable interpolation.
[...]
A long standing alternative is to use Fourier synthesis to generate the waveforms - basically just adding up a bunch of sine waves with different amplitudes and frequencies. This is widely used in software instruments as well as hardware ranging from the classic synths such as the Kawai K5000 to modern designs such as the Novation Peak.
[...]
For the four viper plugins, two possible additive architectures were looked at: an overlapped inverse FFT and a brute-force bank of 128 sine-wave oscillators.
[...]
The alternative oscillator-bank approach, however, is trivially optimisable with inline assembler, and turns out to be capable of rendering waveforms with 128 harmonics in real-time - just enough to be useful.
[...]
macros for loop unrolling. The following assembly
[...]
of these models, under
[...]
The harmonic amplitudes are specified by an array of signed 16 bit value, allowing good resolution. The use of signed amplitudes allows each harmonic to have an effective phase angle of 0 or 180 degrees. The restriction on the phase angle is largely a non-issue, as human hearing is very insensitive to harmonic phase. Curiously, it appears that both the DW-8000 and K3 waveforms appear to be constructed entirely from harmonics with the same restriction, suggesting that both waveform sets were generated algorithmically rather than sampled.
[...]
The set of four viper plugins use several variants the above code, including a version with sparse harmonics (fewer harmonics in total, but each with an independent frequency offset), and a version that uses 8 bit harmonic amplitudes which can be smoothly blended to generate wavetable sweeps for the PPG Wave.
[...]
For each of the emulated instruments, an analyser was written that processes the original waveforms, converting them to harmonic form, selecting the appropriate model, and determining which multi-samples should be preserved (ie those with tonal changes that are not related to anti-aliasing). The analysers mostly rely on straightforward Fourier analysis, but in some cases more work was needed. For example, it turns out that some of the original PPG waves appear to contain numerical overflow errors, where amplitudes had exceeded the range of the 8 bit samples and wrapped:
[...]
To keep within
[...]
Overall, viper produces extremely accurate renditions of the original sounds, struggling only when the original samples contain large non-bandwidth limited transients. The PPG models are the most stressed by this, as the memory footprint limits the number of harmonics and the bit-depth of the harmonic amplitudes - and some of the waveforms appear to contain inadvertent numerical wrapping that would cause strong aliasing that is simply not possible to replicate with the available number of harmonics short of trying to deliberately clip the output waveforms. But the majority of waves are bandwidth limited and render accurately even with the limitations of the Logue platform.
[...]
It is not clear where this will go next. The viper plugins probably push the current Logue instruments as far as they can go for PCM-like waveforms and wavetables. Importantly, the oscillators sound clean and musical and the primary limitations come from the speed and memory size of the Logue platform itself.
[...]
There remains one as yet unreleased variant that pairs a wavetable oscillator with a sweepable 128 band formant filter, roughly analogous to a pair of Kawai K5000 additive sources. However this still needs work to complete a graphical editor for the tables.
- **GammaLib: GFftWavetable Class Reference** (http://cta.irap.omp.eu/gammalib/doxygen/classGFftWavetable.html)
  Lookup table class for Fast Fourier Transformation. More...
[...]
| GFftWavetable (void) |
| --- |
| Void constructor. |
| GFftWavetable (const int &size) |
| Wave table constructor. |
| GFftWavetable (const GFftWavetable&wavetable) |
| Copy constructor. |
| virtual | ~GFftWavetable (void) |
| Destructor. |
| GFftWavetable& | operator= (const GFftWavetable&wavetable) |
[...]
| Assignment operator. |
| std::complex< double > & | operator[] (const int &index) |
[...]
| Return reference to trigonometric coefficient. |
| const std::complex< double > & | operator[] (const int &index) const |
| Return reference to trigonometric coefficient (const version) |
| void | clear (void) |
| Clear lookup table for Fast Fourier Transform. |
| GFftWavetable* | clone (void) const |
| Clone lookup table for Fast Fourier Transform. |
| std::string | classname (void) const |
[...]
| Return class name. |
| int | size (void) const |
| Return number of trigonometric coefficients. |
| const int & | index (const int &factor) const |
| Return start index for a given factor. |
| int | factors (void) const |
| Return number of factorisation factors. |
| int | factor (const int &index) const |
| Return factorisation factor. |
| std::string | print (const GChatter&chatter= NORMAL) const |
| Print lookup table for Fast Fourier Transform information. |
| Public Member Functions inherited from GBase |
| virtual | ~GBase(void) |
| Destructor. |
[...]
| void | init_members (void) |
| --- | --- |
| Initialise class members. |
| void | copy_members (const GFftWavetable&wavetable) |
| Copy class members. |
| void | free_members (void) |
| Delete class members. |
| void | set_members (const int &n) |
| Set wavetable. |
| void | set_factors (const int &n) |
| Compute FFT factorisation. |
[...]
| std::vector< int > | m_factors |
| --- | --- |
| Wavetable factors. |
| std::vector< int > | m_twiddle |
| Start index of factors. |
| std::vector< std::complex< double > > | m_trig |
| Trigonometric coefficients. |
[...]
Lookup table class for Fast Fourier Transformation.
[...]
Constructs the lookup table for Fast Fourier Transformation for a given array`size`.
[...]
Returns the index of the first trigonometric coefficient for a given`factor`.
[...]
## ◆ set_factors()
[...]
Compute FFT factorisation.
[...]
Computes the factorisation
[...]
\[ n = \prod_i p_i \]
[...]
of an array of length`n`.
[...]
The method is a C++ implementation of the fft_complex_factorize() and the fft_factorize() functions of the GSL library (version 2.2.1), defined in the file fft/factorize.c.
[...]
## ◆ set_members
[...]
Computes the coefficients
[...]
\[ \frac{-i 2 \pi j k}{n} \]
[...]
of the discrete Fourier transformation
[...]
\[ x_j = \sum_{k=0}^{n-1} z_k \exp \left( \frac{-i 2 \pi j k}{n} \right) \]
[...]
The method is a C++ implementation of the gsl_fft_complex_wavetable_alloc() function of the GSL library (version 2.2.1), defined in the file fft/c_init.c.
[...]
Trigonometric coefficients.
- **Fast Fourier Transform (FFT) on the ESP32 - Elektor Magazine** (https://www.elektormagazine.com/articles/fast-fourier-transform-fft-on-the-esp32)
  In this project, the Pmod I2S2 module is used, which is cheap and widely available from various sources. A signal (sine wave, square wave, or any other waveform) is injected into its input port (blue connector). The fundamental frequency, magnitude, and musical tone frequency nearest this frequency are all displayed in Serial Monitor. The aim of this project is to show how the Arduino Audio Tools library can be used for an FFT application running on an ESP32. The dev board used throughout the abovementioned book is the Espressif ESP32-WROOM-32D pictured in Figure 1. It’s breadboard compatible and has 5 mm × 27.9 mm dimensions.
[...]
Listing 1: Program to execute FFT on the ESP32. /*************************************************************** * FAST FOURIER TRANSFORM * ====================== * * This program uses the Arduino Audio Tools library to display * the FFT components of an input signal. * * Author : Dogan Ibrahim * Program: FFT * Date : May, 2023 * * (This program is a modified version of the program developed * by Phil Pschatzmann) ***************************************************************/ #include "AudioTools.h" #include "AudioLibs/AudioRealFFT.h" uint16_t sample_rate=44100; uint8_t channels = 2; I2SStream in; AudioRealFFT fft; StreamCopy copier(fft, in); // // Display fft result on Serial Monitor // void fftResult(AudioFFTBase &fft) { float diff; auto result = fft.result(); if (result.magnitude>100) { Serial.print(result.frequency); Serial.print(" "); Serial.print(result.magnitude); Serial.print(" => "); Serial.print(result.frequencyAsNote(diff)); Serial.print( " diff: "); Serial.println(diff); } } void setup(void) { Serial.begin(115200); // // Configure in stream // auto configin = in.defaultConfig(RX_MODE); configin.sample_rate = sample_rate; configin.channels = channels; configin.bits_per_sample = 16; configin.i2s_format = I2S_STD_FORMAT; configin.is_master = true; configin.port_no = 0; configin.pin_ws = 15; // LRCK configin.pin_bck = 14; // SCLK configin.pin_data = 22; // SDOUT configin.pin_mck = 0; in.begin(configin); // // Configure FFT // auto tcfg = fft.defaultConfig(); tcfg.length = 8192; tcfg.channels = channels; tcfg.sample_rate = sample_rate; tcfg.bits_per_sample = 16; tcfg.callback = &fftResult; fft.begin(tcfg); } // // Copy input signal to fft // void loop() { copier.copy(); }
[...]
At the beginning of the program, header files AudioTools.h and AudioLibs/AudioRealFFT.h are included. Streams fft and in are defined. Inside the setup() function, the I2S stream in (in RX_MODE) is configured by specifying the ESP32 GPIO ports it is connected to. Also, the FFT is configured by setting the length to 8,192 samples. The callback function is passed the name fftResult so that the FFT results are available at this function. Function fftResult() displays the frequency, magnitude, and the musical tone nearest this frequency, together with the difference between the two frequencies.
- **modules/fft/include/dsps_fft4r.h at master · espressif/esp-dsp** (https://github.com/espressif/esp-dsp/blob/master/modules/fft/include/dsps_fft4r.h)
  /**
 * @brief      init fft tables
 *
 * Initialization of Complex FFT Radix-4. This function initialize coefficients table.
 * The implementation use ANSI C and could be compiled and run on any platform
 *
 * @param[inout] fft_table_buff: pointer to floating point buffer where sin/cos table will be stored
 *                          if this parameter set to NULL, and table_size value is more then 0, then
 *                          dsps_fft4r_init_fc32 will allocate buffer internally
 * @param[in] max_fft_
[...]
: maximum fft
[...]
/cos table that will be used for radix-4 it's
 *                          four times maximum length of FFT
[...]
*                          if fft_table_buff is NULL and table_size is not 0, buffer will be allocated internally.
 *                          If table_size is 0, buffer will not be allocated.
 *
 * @return
 *      - ESP_OK on success
 *      - ESP_
[...]
_DSP_
[...]
_OUTOFRANGE if table_size > CONFIG_DSP_MAX_FFT_SIZE
 *      - ESP_ERR_DSP_REINITIALIZED if buffer already allocated internally by other function
 *      - One of the error codes
[...]
DSP library
 */
esp_
[...]
/**
 * @brief      complex FFT of radix 4
 *
 * Complex FFT of radix 4
 * The extension (_ansi) use ANSI C and could be compiled and run on any platform.
 * The extension (_ae32) is optimized for ESP32 chip.
 *
 * @param[inout] data: input/output complex array. An elements located: Re[0], Im[0], ... Re[N-1], Im[N-1]
 *               result of FFT will be stored to this array.
 * @param[in] N: Number of complex elements in input array
 * @param[in] table: pointer to sin/cos table
 * @param[in] table_size: size of the sin/cos table
 *
 * @return
 *      - ESP_OK on success
 *      - One of the error codes from DSP library
 */
esp_err_t dsps_fft4r_fc32_ansi_(float *data, int N, float *table, int table_size);
[...]
esp_err_t dsps_fft4r_fc32_ae32_(float *data, int N, float *table, int table_size);
esp_err_t dsps_fft4r_fc32_aes3_(float *data, int N, float *table, int table_size);
esp_err_t dsps_fft4r_fc32_arp4_(float *data, int N, float *table, int table_size);
[...]
/**
 * @brief      bit reverse operation for the complex input array radix-4
 *
 * Bit reverse operation for the complex input array
 * The implementation use ANSI C and could be compiled and run on any platform
 *
 * @param[inout] data: input/ complex array. An elements located: Re[0], Im[0], ... Re[N-1], Im[N-1]
 *               result of FFT will be stored to this array.
 * @param[in] N: Number of complex elements in input array
 *
 * @return
 *      - ESP_OK on success
 *      - One of the error codes from DSP library
 */
[...]
esp_err_t dsps_bit_rev4r_fc32(float *data, int N);
[...]
/**
 * @brief      Convert FFT result to complex array for real input data
 *
 * Convert FFT result of complex FFT for real input to complex output.
 * This function have to be used if FFT used to process real data.
 * This function use tables inside and can be used only it dsps_fft4r_init_fc32(...) was
 * called and FFT4 was initialized.
 * The implementation use ANSI C and could be compiled and run on any platform
 *
 * @param[inout] data: Input complex array and result of FFT2R/FFT4R.
 *               input has size of 2*N, because contains real and imaginary part.
 *               result will be stored to the same array.
 *               Input1: input[0..N-1] if the result is complex Re[0], Im[0]....Re[N-1], Im[N-1],
 *                       and input[0...2*n-1] if result is real re[0], re[1],...,re[2*N-1].
 * @param[in] N: Number of complex elements in input array
 * @param[in] table: pointer to sin/cos table
 * @param[in] table_size: size of the sin/cos table
 *
 * @return
 *      - ESP_OK on success
 *      - One of the error codes from DSP library
 */
esp_err_t dsps_cplx2real_fc32_ansi_(float *data, int N, float *table, int table_size);
[...]
esp_err_t dsps_cplx2real_fc32_ae32_(float *data, int N, float *table, int table_size);
[...]
_table(int N, int step, char

### Notes/Observations
- Investigating population methods and memory reduction strategies.
