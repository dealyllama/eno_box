     1|
     2|
     3|## Research Session: 2026-05-09 11:38:21
     4|**Focus:** Waveform Generation & Compression Techniques
     5|
     6|### Raw Findings
     7|#### Topic: Efficient waveform generation algorithms (Sine, Saw, Square, Triangle) for wavetable synthesis
     8|- **Table Lookup Oscillator | RingBuffer** (http://ringbuffer.org/sound_synthesis_introduction/Processed_Recording/table_lookup_oscillator/)
     9|  Table-lookup oscillators are essential in digital signal processing and audio synthesis. They are widely used to generate periodic waveforms such as sine, square, triangle, and sawtooth waves. Beyond that, the method can be used to sythesize arbitrary waveforms, which is its key strength.
    10|[...]
    11|- Flexibility: The same framework can generate different waveforms - and thus any timbre - by using different tables.
    12|- Efficiency: By precomputing a waveform and storing it in a lookup table, the computation during runtime is minimal.
    13|[...]
    14|noise. For many applications, a well-designed table is accurate enough.
    15|[...]
    16|- Memory Usage: This is usually not a problem, but the more precise a
    17|[...]
    18|A lookup table is a sampled version of an arbitray continuous waveform. For basic periodic waveforms, the table holds exactly one cycle, sometimes only half or the quarter of a cycle, making use of symmetries. Table sizes of 256-4096 samples are common in sound synthesis applications.
    19|[...]
    20|This method in 3 steps ensures smooth waveform generation with high accuracy and efficiency, suitable for real-time audio or signal processing applications.
    21|[...]
    22|- This smooths out the waveform, especially for higher frequencies or low-resolution tables.
    23|- When the phasor lands between two indices in the table, linear interpolation is used to compute an in-between value.
    24|- The fractional part of the phase is used for interpolation between the current and next index.
    25|- The integer part of the phase determines the index in the table.
    26|[...]
    27|## Efficiency¶
    28|[...]
    29|### Efficiency of Lookup Table Oscillators¶
    30|[...]
    31|Generating a waveform involves:
    32|[...]
    33|- Simple arithmetic: Updating the phase index and performing modulo operations.
    34|- Memory access: Fetching a value from an array.
    35|[...]
    36|- These calculations require multiple multiplications, additions, and sometimes divisions, making them computationally expensive.
    37|- Trigonometric calculations: Implementations use Taylor series, CORDIC algorithms, or similar methods.
    38|[...]
    39|- `sin()` Function: ( O(n) ), where ( n ) depends on the precision required.
    40|- Lookup Table Oscillator: ( O(1) ) per sample (array lookup + basic arithmetic).
    41|[...]
    42|- This results in 10–100x improvement in speed for a lookup table oscillator.
    43|- A lookup table operation (memory access + arithmetic) might take 1–5 nanoseconds [2][3].
    44|- A single`sin()` computation can take 50–150 nanoseconds [1].
    45|[...]
    46|- Total computation time: ~0.1 ms.
    47|- Perform ( 44,100 ) memory accesses and phase updates, each taking ~1–5 ns.
    48|[...]
    49|The Signal-to-Noise Ratio (SNR) and quantization noise for a table lookup sine oscillator depend on the table size N, which is the number of samples per cycle used in the lookup table. A larger table size improves the accuracy of the sine wave representation and thus reduces the quantization error (noise) and increases the SNR.
    50|[...]
    51|The Signal-to-Noise Ratio (SNR) for a table lookup sine oscillator is influenced by the size of the lookup table and the interpolation method used. Larger tables result in more accurate sine wave approximations, while interpolation techniques (like linear or cubic interpolation) reduce quantization noise, improving the overall SNR. The quantization in the time domain has the same effect as a quantization in the amplitude range.
    52|[...]
    53|#### Effect of Interpolation:¶
    54|[...]
    55|- For even higher precision, cubic interpolation can be used, but at a greater computational expense.
    56|- Linear interpolation provides a balance between computational cost and accuracy.
    57|- Without interpolation, the output waveform can sound rough or distorted, especially when the table resolution is low.
    58|- Even with interpolation, lookup tables remain faster than direct computation.
    59|- Lookup tables can use interpolation (e.g., linear or cubic) to improve accuracy, adding some computational overhead.
    60|[...]
    61|Linear interpolation reduces the quantization noise compared to using no interpolation, improving the SNR by approximately 3 dB.
    62|- **Software Synthesis** (https://basicsynth.com/index.php?page=wvtables)
    63|  A general principle of computer programming is that we can trade memory for computational speed. If we store a waveform in a table, we can then index into the table to get the values rather than repeatedly calculating them. For periodic waveforms we only have to calculate one cycle of the waveform, an even greater savings. We can produce a continuous sound if we loop back to the beginning of the table when we reach the end. In other words, we increment the table index modulo the table length.
    64|[...]
    65|Given that the table contains one complete cycle of the waveform, the table length represents 2π radians. Thus, with a fixed table length, we calculate the increment value for the index by dividing the table length by the samples per period. This will give us an index value that will cause the code to skip through the table at different rates for different frequencies. We must allocate a table and fill it with a sin wave as before, but now we can keep the table in memory at all times. Generating the sound is reduced to calculating an index and retrieving the sample values from the table.
    66|[...]
    67|The wavetable does not have to be filled with a sine wave. We can sum sine waves together when we initialize the table, or even load the waveform from a file to playback a recorded or pre-computed waveform. We can also change between wavetables during sound generation to produce "morphing" of the waveform. Furthermore, the wavetable does not have to be limited to one cycle of the waveform. If we have a waveform that varies over some regular number of periods, we can still build a table for that waveform if we include multiple periods. However, when more than one period is included, the increment must be multiplied by the number of periods to produce the correct frequency.
    68|[...]
    69|Using this type of interpolation adds some computation time, but is still much faster than repeatedly recalculating the waveform using the*sin*function.However, there is a possibility to get nearly the same results without interpolation. The amount of error in the sample value will depend in large part on the table length. For short tables, we have a small set of index values to select from and will be more likely to introduce an error than with a longer table.
    70|[...]
    71|The table index value has a fixed number of bits available to represent the integer and fractional parts of the index. With longer tables, the index varies over a larger range and thus more bits are contained in the integer portion of the index. For example, with a table of 256 values, only 8 bits of the index are used for the integer portion. For a table of 512 values, 9 bits are used, etc. With each doubling of the table length we gain one bit of precision in the index and are discarding one less bit of precision to obtain an integer index. This results in a smaller variation between the lookup value and the directly calculated value. In effect, lengthening the table is much like performing interpolation on a shorter table once rather than for each sample. Lengthening the table also means we have fewer bits in the fractional portion of the index and the quality of the interpolation diminishes. Thus, for shorter tables (4k or less) it is best to use interpolation, but for longer tables it is sufficient to simpy round-off the index. A table of 16K size generally works very well. Note that to make best use of the index range, table length should be a power of two.
    72|- **Niels Moseley's bloggy bits** (https://namoseley.wordpress.com/2015/07/26/sincos-generation-using-table-lookup-and-iterpolation/)
    73|  In the past, I’ve written several blog posts on calculating sin(x) and cos(x) using polynomials. These methods can be very accurate but require many multiplications and additions. For usage in hand-held calculators, where a few evaluations per second are enough, these algorithms are fine. However, in high-speed signal processing and generation applications, such as software-defined radio or test equipment, a less computationally intensive method is required.
    74|[...]
    75|A simple way to generate waveforms is to read the contents of a table, which resides in memory, at a constant speed. Usually, a single cycle of the desired waveform is stored in the table. When the table hasNnumber of entries and the table entries are read at a speed ofF_sentries per second, the frequency of the generated waveform will beF=F_s/N Hz.
    76|[...]
    77|# Algorithm #1: Table lookup
    78|[...]
    79|To generate a sine (or cosine) wave, we must simply fill the table with sin(x). The table entries(k=0 \ldots N-1)are thus:table[k] = \sin \left (2 \cdot \pi \cdot \frac{k}{N} \right ).
    80|[...]
    81|reached. This
    82|[...]
    83|. This way,
    84|[...]
    85|# Algorithm #2: Table lookup and interpolation
    86|[...]
    87|We can easily get better results without increasing the number of table entries significantly by interpolating between the table values; we have the fractional part! The following algorithm uses linear interpolation:
    88|[...]
    89|Spectrum of a sine wave generated by the table-lookup with interpolationmethodClearly, the linear interpolation increases the spectral purity significantly! There are fewer spurious responses and the highest one is better than -90 dBc. For software radio applications this good enough.
    90|[...]
    91|# Algorithm #3: Table lookup and circular interpolation
    92|[...]
    93|There are certain applications where -90 dBc of spectral purity isn’t enough, for instance, in test equipment, very high purity digital down-converters are needed for precise measurements, e.g. to reduce noise folding. To increase the spectral purity, we could increase the table size, or we could choose a better interpolation method.
    94|[...]
    95|One way of getting a better interpolation method would be to use following identity:
    96|[...]
    97|\sin(A+B) = \sin(A) \cdot \cos(B) + \cos(A) \cdot sin(B)
    98|[...]
    99|If we choose A to be derived from the upper 8 bits (integer part) of the phase accumulator, we can lookup\sin(A)and\cos(A)using our table and these lookups will be*exact*. Note that\cos(A) = \sin(A + \frac{\pi}{2}), so\cos(A)is simply found 64 entries further in the table. Then, B is the fractional angle, which is represented by the lower 16 bits of the phase accumulator. As the fractional angle is always somewhere between the table entries, we cannot use our lookup table. We could use a second lookup table or we can use the well-known approximations of\sin(x)and\cos(x)for small angles:
   100|[...]
   101|\sin(x) \approx xand\cos(x) \approx 1-\frac{x^2}{2}.
   102|[...]
   103|Spectrum of a sine wave generated by the table-lookup with circular interpolationThe circular interpolation gives an improvement over linear interpolation at the cost of an additional table lookup and a few multiplications and additions. The spurious responses are now found at around -128 dBc, which is 38 dB better.
   104|[...]
   105|Further experiments show that increasing the table size to 512 entries produces spurious responses that are below -147 dBc. A table size of 128 entries gives responses below -110 dBc.
   106|[...]
   107|The table-lookup and interpolate method of signal generation is a powerful way to generate high-purity signals without resorting to complex approximations.With simple linear interpolation, a 256-entry table can produce sine and cosine waveforms with spurious responses that are around 90 dB below the carrier. At the expense of a few additional operations, spurious responses can be as low as -128 dBc by using circular interpolation.
   108|[...]
   109|The presented algorithms are also well suited to generate quadrature signals as both sine and cosine waveforms can be generated simultaneously.
   110|- **Wavetable Synthesis Algorithm Explained | WolfSound** (https://thewolfsound.com/sound-synthesis/wavetable-synthesis-algorithm/)
   111|  - how is wavetable synthesis related to other synthesis methods.
   112|- what are pros and cons of wavetable synthesis, and
   113|- step-by-step wavetable synthesis algorithm (also known as fixed-waveform synthesis [7]),
   114|- how to generate sound using wave tables,
   115|[...]
   116|## A Need for a Fast and Efficient Synthesis Method
   117|[...]
   118|In the early days of digital sound synthesis, sound was synthesised using specialized digital signal processing hardware. Later on, the community started using software for the same purposes but the underlying principles and algorithms remained the same. To obtain real-time performance capabilities with that technology, there was a great need to generate sound efficiently in terms of memory and processing speed. Thus, the wavetable technique was convceived: it is both fast and memory-inexpensive.
   119|[...]
   120|A gesture provides control information. In the case of pressing a key on a MIDI keyboard, control information would incorporate information on which key was pressed and how fast was it pressed (velocity of a keystroke). We can change the note number information into frequency ff and the velocity information into amplitude AA. This information is sufficient to generate sound using most of the popular synthesis algorithms.
   121|[...]
   122|## Sine Generator
   123|[...]
   124|Let’s imagine that given frequency and amplitude information we want to generate a sine wave. The general formula of a sine waveform is
   125|[...]
   126|s(t)=Asin⁡(2πft+ϕ),(1)s(t) = A \sin (2 \pi f t + \phi), \quad (1)
   127|[...]
   128|`sin()` calls use the Taylor expansion of the sine function [1]
   129|[...]
   130|Above expansion is infinite, so on real-world hardware, it needs to be truncated at some point (after obtaining sufficient accuracy). Its advantage is, that it uses operations realizable in hardware (multiplication, division, addition, subtraction). Its disadvantage is that it involves a lot of these operations. If we need to produce 44100 samples per second and want to play a few hundred sines simultaneously (what is typical of additive synthesis), we need to be able to compute the sin⁡\sin function more efficiently than with Taylor expansion.
   131|[...]
   132|## A Wave Table
   133|[...]
   134|A wave table is an array in memory in which we store a fragment of a waveform. A waveform is a plot of a signal over time. Thus, one period of a sine wave stored in memory looks as follows:
   135|[...]
   136|The above wave table uses 64 samples to store one period of the sine wave. These values can be calculated using the Taylor expansion because we compute them only once and store them in memory.
   137|[...]
   138|sin⁡\sin period is exactly 2π2 \pi. The period of a wave table is its length, let’s denote it by LL. For each sample index k∈{0,…,L−1}k \in \{0, \dots, L-1\} in the wave table, there exists a corresponding argument θ∈[0,2π)\theta \in [0, 2\pi) of the sine function.
   139|[...]
   140|kL=θ2π.(5)\frac{k}{L} = \frac{\theta}{2 \pi}. \quad (5)
   141|[...]
   142|## Computing a Waveform Value from the Wave Table
   143|[...]
   144|Equation 5 holds for θ∈[0,2π)\theta \in [0, 2\pi). If we want to calculate the values of arbitrary x∈Rx \in \mathbb{R}, we need to remove the multiplicity of 2π2 \pi contained in xx to bring it to the [0,2π)[0, 2\pi) range. In other words, if
   145|[...]
   146|x=2πl+ϕx,ϕx∈[0,2π),(6)x = 2\pi l + \phi_x, \quad \phi_x \in [0, 2\pi), \quad (6)
   147|[...]
   148|then we want to find ϕx\phi_x. In software, it can be done by subtracting or adding 2π2 \pi to xx until we obtain a value in the desired range. Alternatively, we can use a function called`fmod()`, which allows us to obtain the remainder of a floating-point division.
   149|[...]
   150|We can subsequently compute the corresponding index in the wave table from the proportion in Equation 5.
   151|[...]
   152|k=ϕxL2π.(7)k = \frac{\phi_x L}{2\pi}. \quad (7)
   153|[...]
   154|Now`waveTable[k]`
   155|[...]
   156|return the value of sin⁡(x)\sin(x),
   157|[...]
   158|In most cases, kk computed in Equation 7 won’t be an integer. It will rather be a floating-point number between some two integers denoting the wave table indices, i.e., i<=k
   159|- **Quarter sine wave look up table synth with various oscillators in Nim · GitHub** (https://gist.github.com/ingoogni/b115408fb20d58cffa4d09c343a0a057)
   160|  Quarter sine wave look up table synth with various oscillators in Nim
   161|[...]
   162|| import math |
   163|| --- |
   164|| const |
   165|| SampleRate {.intdefine.} = 44100 |
   166|| SRate* = SampleRate.float |
   167|| LutSize = 256 # Quarter sine wave |
   168|| Lut = static: |
   169|| var arr: array[LutSize, float] |
   170|| for i in 0.. = 2.0 * PI: |
   171|| gen.phase -= 2.0 * PI |
   172|| template advanceModPhase*()= |
   173|| gen.phase += modulatedIncrement |
   174|| if gen.phase >= 2.0 * PI: |
   175|| gen.phase -= 2.0 * PI |
   176|[...]
   177|| proc nextSample*(gen: var WaveGenerator): float = |
   178|| # Generate sine wave using quarter LUT |
   179|| result = gen.lookupSine(gen.phase) |
   180|| advancePhase() |
   181|[...]
   182|| proc nextSawtooth*(gen: var WaveGenerator): float = |
   183|| # "Sawtooth" by stepping through LUT linearly (ignoring symmetry) |
   184|| advancePhase() |
   185|| let |
   186|| normalizedPhase = gen.phase / (2.0 * PI) |
   187|| index = int(normalizedPhase * LutSize.float) |
   188|| clampedIndex = min(index, LutSize - 1) |
   189|| return Lut[clampedIndex] * 2.0 - 1.0 # Center around 0 |
   190|[...]
   191|| proc nextTriangle*(gen: var WaveGenerator): float = |
   192|| # Triangle wave using bidirectional LUT traversal |
   193|| advancePhase() |
   194|| let normalizedPhase = gen.phase / (2.0 * PI) |
   195|| if normalizedPhase < 0.5: |
   196|| # Forward through LUT |
   197|| let |
   198|| index = int(normalizedPhase * 2.0 * LutSize.float) |
   199|| clampedIndex = min(index, LutSize - 1) |
   200|| return Lut[clampedIndex] |
   201|| else: |
   202|| # Backward through LUT |
   203|| let |
   204|| index = int((1.0 - normalizedPhase) * 2.0 * LutSize.float) |
   205|| clampedIndex = min(index, LutSize - 1) |
   206|| return Lut[clampedIndex] |
   207|[...]
   208|| proc nextSquare*(gen: var WaveGenerator): float = |
   209|| # Square wave using LUT threshold |
   210|| advancePhase() |
   211|| let |
   212|| normalizedPhase = gen.phase / (2.0 * PI) |
   213|| index = int(normalizedPhase * LutSize.float) |
   214|| clampedIndex = min(index, LutSize - 1) |
   215|| return if Lut[clampedIndex] > 0.5: 1.0 else: -1.0 |
   216|[...]
   217|| proc nextPhaseDistortion*(gen: var WaveGenerator, distAmount
   218|[...]
   219|float = 0.5): float = |
   220|[...]
   221|lookupSine(
   222|[...]
   223|seq[float]): float = |
   224|[...]
   225|advanceModPhase() |
   226|[...]
   227|| proc nextPDSaw*(gen: var WaveGenerator): float = |
   228|| ## PD: Sawtooth-like, using variable rate by making first half of cycle fast, |
   229|| ## second half slow |
   230|| let |
   231|| basePhaseIncrement = 2.0 * PI * gen.freq / gen.sampleRate |
   232|| normalizedPhase = gen.phase / (2.0 * PI) |
   233|| rateMultiplier = if normalizedPhase < 0.5: 3.0 else: 0.2 |
   234|| modulatedIncrement = basePhaseIncrement * rateMultiplier |
   235|| result = gen.lookupSine(gen.phase) |
   236|| advanceModPhase() |
   237|[...]
   238|| proc nextPDSquare*(gen: var WaveGenerator): float = |
   239|| ## PD: Square-like, using phase jumps |
   240|| let |
   241|| basePhaseIncrement = 2.0 * PI * gen.freq / gen.sampleRate |
   242|| normalizedPhase = gen.phase / (2.0 * PI) |
   243|| # Slow down near 0.25 and 0.75 |
   244|| distanceFromPeak1 = abs(normalizedPhase - 0.25) |
   245|| distanceFromPeak2 = abs(normalizedPhase - 0.75) |
   246|| nearPeak = min(distanceFromPeak1, distanceFromPeak2) |
   247|| # Speed up elsewhere |
   248|| rateMultiplier = if nearPeak < 0.1: 0.1 else: 2.0 |
   249|| modulatedIncrement = basePhaseIncrement * rateMultiplier |
   250|| result = gen.lookupSine(gen.phase) |
   251|| gen.phase += modulatedIncrement |
   252|| if gen.phase >= 2.0 * PI: |
   253|| gen.phase -= 2.0 * PI |
   254|[...]
   255|| when isMainModule: |
   256|[...]
   257|| var gen = initWaveGenerator() |
   258|| gen.setFrequency(440.0) # A4 |
   259|[...]
   260|| #var tickerTape = Ticker(tick: 0) |
   261|| var sample = 0.0 |
   262|[...]
   263|| proc tick*(): float = |
   264|| sample = gen.nextSample() |
   265|| #sample = gen.nextSawtooth() |
   266|| #sample = gen.nextTriangle() |
   267|| #sample = gen.nextSquare() |
   268|| #sample = gen.nextPhaseDistortion(0.5) |
   269|| #sample = gen.nextResonantSweep(0.8) |
   270|| #let czSegments = @[2.0, 0.1, 0.5, 0.3, 1.5, 0.7] |
   271|| #sample = gen.nextCZStyle(czSegments) |
   272|| #sample = gen.nextPDSaw() |
   273|| #sample = gen.nextPDSquare() |
   274|| #sample = gen.nextRingMod(100.0) |
   275|| #sample = gen.nextAM(110.0, 0.9) |
   276|| #sample = gen.nextWaveshaped(3.0) |
   277|| #sample = gen.nextDistorted(0) |
   278|| #sample = gen.nextDistorted(1) |
   279|| #sample = gen.nextDistorted(2) |
   280|[...]
   281|| #inc tickerTape.tick |
   282|[...]
   283|| return sample |
   284|
   285|#### Topic: Compressed wavetable synthesis using Fourier coefficients for ESP32/embedded systems
   286|- **danilogcrf2-oss/ESP32Synth** (https://github.com/danilogcrf2-oss/ESP32Synth)
   287|  - **Wavetable Synthesis:** Use custom wavetables for unique, complex timbres. The engine supports both direct assignment and a memory-efficient registry system for tracker instruments.
   288|[...]
   289|### Wavetables: `setWavetable()`, `registerWavetable()`
   290|[...]
   291|Wavetables are arrays containing a single cycle of a custom waveform.
   292|[...]
   293|- `void setWavetable(uint8_t voice, const void* data, uint32_t size, BitDepth depth);`
   294|[...]
   295|* Assigns a wavetable directly to a single voice.
   296| * `data`: Pointer to the array of sample data.
   297| * `size`: The number of samples in the array.
   298| * `depth`: The bit depth of the data (`BITS_8` or `BITS_16`).
   299|[...]
   300|- `void registerWavetable(uint16_t id, const void* data, uint32_t size, BitDepth depth);`
   301|[...]
   302|* Registers a wavetable in a global lookup table with a unique `id` (0-999). This is the required method for using wavetables with **Tracker-Style Instruments**. It is highly memory-efficient as the wavetable is stored only once and referenced by its ID.
   303|[...]
   304|- `tools/Wavetables/WavetableMaker.py`: Generates wavetables from various sources.
   305|- `tools/Samples/WavToEsp32SynthConverter.py`: Converts standard `.wav` files into `.h` header files compatible with the sampler engine.
   306|[...]
   307|## Advanced Usage: Wavetable Memory Management
   308|[...]
   309|A critical detail when working with wavetables is understanding the difference between the number of samples and the size of the data array in bytes. The `setWavetable()` and `registerWavetable()` functions expect the `size` parameter to be the **number of individual samples** in the waveform, not the byte size of the array.
   310|[...]
   311|When using C++'s `sizeof()` operator, you get the total size of the array in bytes. To get the correct number of samples, you must adjust this value based on the wavetable's bit depth.
   312|[...]
   313|### `BITS_16` (16-bit wavetables)
   314|[...]
   315|Each sample is an `int16_t`, which takes up 2 bytes. To
   316|[...]
   317|number of samples, you must **divide by 2**.
   318|[...]
   319|### `BITS_8` (8-bit wavetables)
   320|[...]
   321|### `BITS_4` (4-bit wavetables)
   322|[...]
   323|4-bit samples are packed, with **two samples stored in a single `uint8_t`**. To get the total number of samples, you must **multiply by 2**.
   324|[...]
   325|```cpp
   326|// A 4-bit wavetable with 256 samples occupies 128 bytes.
   327|const uint8_t my_wave_4bit[128] = { ... };
   328|[...]
   329|// Correct usage:
   330|synth.setWavetable(0, my_wave_4bit, sizeof(my_wave_4bit) * 2, BITS_4);
   331|```
   332|[...]
   333|`setWavetable
   334|[...]
   335|e `registerWavetable()` esperam que o
   336|[...]
   337|metro `size` seja o **número de amostras individuais** na forma de onda, e não o tamanho do array em bytes
   338|[...]
   339|### `BITS_16` (
   340|[...]
   341|16-bit)
   342|[...]
   343|`BITS_4` (
   344|[...]
   345|etables de 4-bit)
   346|[...]
   347|As amostras de 4
   348|[...]
   349|uint8_t`**. Para
   350|[...]
   351|- **Síntese Wavetable:** Use wavetables customizadas para timbres únicos e complexos. O motor suporta tanto atribuição direta quanto um sistema de registro eficiente em memória para instrumentos de tracker.
   352|[...]
   353|### Wavetables: `setWavetable()`, `registerWavetable()`
   354|[...]
   355|Wavetables são arrays contendo um único ciclo de uma forma de onda personalizada.
   356|[...]
   357|- `void setWavetable(uint8_t voice, const void* data, uint32_t size, BitDepth depth);`
   358|[...]
   359|* Atribui uma wavetable diretamente a uma única voz.
   360| * `data`: Ponteiro para o array de dados da amostra.
   361| * `size`: O número de amostras no array.
   362| * `depth`: A profundidade de bits dos dados (`BITS_8` ou `BITS_16`).
   363|[...]
   364|- `void registerWavetable(uint16_t id, const void* data, uint32_t size, BitDepth depth);`
   365|[...]
   366|* Registra uma wavetable em uma tabela de pesquisa global com um `id` único (0-999). Este é o método necessário para usar wavetables com **Instrumentos Estilo Tracker**. É altamente eficiente em memória, pois a wavetable é armazenada apenas uma vez e referenciada pelo seu ID.
   367|[...]
   368|- `tools/Wavetables/WavetableMaker.py`: Gera wavetables a partir de várias fontes.
   369|- `tools/Samples/WavToEsp32SynthConverter.py`: Converte arquivos `.wav` padrão em arquivos de cabeçalho `.h` compatíveis com o motor de sampler.
   370|- **Additive synthesis on a CPU and memory constrained ARM platform - tsoniq** (https://tsoniq.net/blog/2023/viper-additive-synthesis/)
   371|  plugins like fusion and phase7 use variations of the PolyBLEP family of algorithms.
   372|[...]
   373|simple waveforms such as saw or square waves, but are difficult and computationally
   374|[...]
   375|with more complex waveforms such as are produced by wavetable interpolation.
   376|[...]
   377|A long standing alternative is to use Fourier synthesis to generate the waveforms - basically just adding up a bunch of sine waves with different amplitudes and frequencies. This is widely used in software instruments as well as hardware ranging from the classic synths such as the Kawai K5000 to modern designs such as the Novation Peak.
   378|[...]
   379|For the four viper plugins, two possible additive architectures were looked at: an overlapped inverse FFT and a brute-force bank of 128 sine-wave oscillators.
   380|[...]
   381|The alternative oscillator-bank approach, however, is trivially optimisable with inline assembler, and turns out to be capable of rendering waveforms with 128 harmonics in real-time - just enough to be useful.
   382|[...]
   383|macros for loop unrolling. The following assembly
   384|[...]
   385|of these models, under
   386|[...]
   387|The harmonic amplitudes are specified by an array of signed 16 bit value, allowing good resolution. The use of signed amplitudes allows each harmonic to have an effective phase angle of 0 or 180 degrees. The restriction on the phase angle is largely a non-issue, as human hearing is very insensitive to harmonic phase. Curiously, it appears that both the DW-8000 and K3 waveforms appear to be constructed entirely from harmonics with the same restriction, suggesting that both waveform sets were generated algorithmically rather than sampled.
   388|[...]
   389|The set of four viper plugins use several variants the above code, including a version with sparse harmonics (fewer harmonics in total, but each with an independent frequency offset), and a version that uses 8 bit harmonic amplitudes which can be smoothly blended to generate wavetable sweeps for the PPG Wave.
   390|[...]
   391|For each of the emulated instruments, an analyser was written that processes the original waveforms, converting them to harmonic form, selecting the appropriate model, and determining which multi-samples should be preserved (ie those with tonal changes that are not related to anti-aliasing). The analysers mostly rely on straightforward Fourier analysis, but in some cases more work was needed. For example, it turns out that some of the original PPG waves appear to contain numerical overflow errors, where amplitudes had exceeded the range of the 8 bit samples and wrapped:
   392|[...]
   393|To keep within
   394|[...]
   395|Overall, viper produces extremely accurate renditions of the original sounds, struggling only when the original samples contain large non-bandwidth limited transients. The PPG models are the most stressed by this, as the memory footprint limits the number of harmonics and the bit-depth of the harmonic amplitudes - and some of the waveforms appear to contain inadvertent numerical wrapping that would cause strong aliasing that is simply not possible to replicate with the available number of harmonics short of trying to deliberately clip the output waveforms. But the majority of waves are bandwidth limited and render accurately even with the limitations of the Logue platform.
   396|[...]
   397|It is not clear where this will go next. The viper plugins probably push the current Logue instruments as far as they can go for PCM-like waveforms and wavetables. Importantly, the oscillators sound clean and musical and the primary limitations come from the speed and memory size of the Logue platform itself.
   398|[...]
   399|There remains one as yet unreleased variant that pairs a wavetable oscillator with a sweepable 128 band formant filter, roughly analogous to a pair of Kawai K5000 additive sources. However this still needs work to complete a graphical editor for the tables.
   400|- **GammaLib: GFftWavetable Class Reference** (http://cta.irap.omp.eu/gammalib/doxygen/classGFftWavetable.html)
   401|  Lookup table class for Fast Fourier Transformation. More...
   402|[...]
   403|| GFftWavetable (void) |
   404|| --- |
   405|| Void constructor. |
   406|| GFftWavetable (const int &size) |
   407|| Wave table constructor. |
   408|| GFftWavetable (const GFftWavetable&wavetable) |
   409|| Copy constructor. |
   410|| virtual | ~GFftWavetable (void) |
   411|| Destructor. |
   412|| GFftWavetable& | operator= (const GFftWavetable&wavetable) |
   413|[...]
   414|| Assignment operator. |
   415|| std::complex< double > & | operator[] (const int &index) |
   416|[...]
   417|| Return reference to trigonometric coefficient. |
   418|| const std::complex< double > & | operator[] (const int &index) const |
   419|| Return reference to trigonometric coefficient (const version) |
   420|| void | clear (void) |
   421|| Clear lookup table for Fast Fourier Transform. |
   422|| GFftWavetable* | clone (void) const |
   423|| Clone lookup table for Fast Fourier Transform. |
   424|| std::string | classname (void) const |
   425|[...]
   426|| Return class name. |
   427|| int | size (void) const |
   428|| Return number of trigonometric coefficients. |
   429|| const int & | index (const int &factor) const |
   430|| Return start index for a given factor. |
   431|| int | factors (void) const |
   432|| Return number of factorisation factors. |
   433|| int | factor (const int &index) const |
   434|| Return factorisation factor. |
   435|| std::string | print (const GChatter&chatter= NORMAL) const |
   436|| Print lookup table for Fast Fourier Transform information. |
   437|| Public Member Functions inherited from GBase |
   438|| virtual | ~GBase(void) |
   439|| Destructor. |
   440|[...]
   441|| void | init_members (void) |
   442|| --- | --- |
   443|| Initialise class members. |
   444|| void | copy_members (const GFftWavetable&wavetable) |
   445|| Copy class members. |
   446|| void | free_members (void) |
   447|| Delete class members. |
   448|| void | set_members (const int &n) |
   449|| Set wavetable. |
   450|| void | set_factors (const int &n) |
   451|| Compute FFT factorisation. |
   452|[...]
   453|| std::vector< int > | m_factors |
   454|| --- | --- |
   455|| Wavetable factors. |
   456|| std::vector< int > | m_twiddle |
   457|| Start index of factors. |
   458|| std::vector< std::complex< double > > | m_trig |
   459|| Trigonometric coefficients. |
   460|[...]
   461|Lookup table class for Fast Fourier Transformation.
   462|[...]
   463|Constructs the lookup table for Fast Fourier Transformation for a given array`size`.
   464|[...]
   465|Returns the index of the first trigonometric coefficient for a given`factor`.
   466|[...]
   467|## ◆ set_factors()
   468|[...]
   469|Compute FFT factorisation.
   470|[...]
   471|Computes the factorisation
   472|[...]
   473|\[ n = \prod_i p_i \]
   474|[...]
   475|of an array of length`n`.
   476|[...]
   477|The method is a C++ implementation of the fft_complex_factorize() and the fft_factorize() functions of the GSL library (version 2.2.1), defined in the file fft/factorize.c.
   478|[...]
   479|## ◆ set_members
   480|[...]
   481|Computes the coefficients
   482|[...]
   483|\[ \frac{-i 2 \pi j k}{n} \]
   484|[...]
   485|of the discrete Fourier transformation
   486|[...]
   487|\[ x_j = \sum_{k=0}^{n-1} z_k \exp \left( \frac{-i 2 \pi j k}{n} \right) \]
   488|[...]
   489|The method is a C++ implementation of the gsl_fft_complex_wavetable_alloc() function of the GSL library (version 2.2.1), defined in the file fft/c_init.c.
   490|[...]
   491|Trigonometric coefficients.
   492|- **Fast Fourier Transform (FFT) on the ESP32 - Elektor Magazine** (https://www.elektormagazine.com/articles/fast-fourier-transform-fft-on-the-esp32)
   493|  In this project, the Pmod I2S2 module is used, which is cheap and widely available from various sources. A signal (sine wave, square wave, or any other waveform) is injected into its input port (blue connector). The fundamental frequency, magnitude, and musical tone frequency nearest this frequency are all displayed in Serial Monitor. The aim of this project is to show how the Arduino Audio Tools library can be used for an FFT application running on an ESP32. The dev board used throughout the abovementioned book is the Espressif ESP32-WROOM-32D pictured in Figure 1. It’s breadboard compatible and has 5 mm × 27.9 mm dimensions.
   494|[...]
   495|Listing 1: Program to execute FFT on the ESP32. /*************************************************************** * FAST FOURIER TRANSFORM * ====================== * * This program uses the Arduino Audio Tools library to display * the FFT components of an input signal. * * Author : Dogan Ibrahim * Program: FFT * Date : May, 2023 * * (This program is a modified version of the program developed * by Phil Pschatzmann) ***************************************************************/ #include "AudioTools.h" #include "AudioLibs/AudioRealFFT.h" uint16_t sample_rate=44100; uint8_t channels = 2; I2SStream in; AudioRealFFT fft; StreamCopy copier(fft, in); // // Display fft result on Serial Monitor // void fftResult(AudioFFTBase &fft) { float diff; auto result = fft.result(); if (result.magnitude>100) { Serial.print(result.frequency); Serial.print(" "); Serial.print(result.magnitude); Serial.print(" => "); Serial.print(result.frequencyAsNote(diff)); Serial.print( " diff: "); Serial.println(diff); } } void setup(void) { Serial.begin(115200); // // Configure in stream // auto configin = in.defaultConfig(RX_MODE); configin.sample_rate = sample_rate; configin.channels = channels; configin.bits_per_sample = 16; configin.i2s_format = I2S_STD_FORMAT; configin.is_master = true; configin.port_no = 0; configin.pin_ws = 15; // LRCK configin.pin_bck = 14; // SCLK configin.pin_data = 22; // SDOUT configin.pin_mck = 0; in.begin(configin); // // Configure FFT // auto tcfg = fft.defaultConfig(); tcfg.length = 8192; tcfg.channels = channels; tcfg.sample_rate = sample_rate; tcfg.bits_per_sample = 16; tcfg.callback = &fftResult; fft.begin(tcfg); } // // Copy input signal to fft // void loop() { copier.copy(); }
   496|[...]
   497|At the beginning of the program, header files AudioTools.h and AudioLibs/AudioRealFFT.h are included. Streams fft and in are defined. Inside the setup() function, the I2S stream in (in RX_MODE) is configured by specifying the ESP32 GPIO ports it is connected to. Also, the FFT is configured by setting the length to 8,192 samples. The callback function is passed the name fftResult so that the FFT results are available at this function. Function fftResult() displays the frequency, magnitude, and the musical tone nearest this frequency, together with the difference between the two frequencies.
   498|- **modules/fft/include/dsps_fft4r.h at master · espressif/esp-dsp** (https://github.com/espressif/esp-dsp/blob/master/modules/fft/include/dsps_fft4r.h)
   499|  /**
   500| * @brief      init fft tables
   501|

## Detailed Extraction: 2026-05-09 15:15:52
### Source: http://ringbuffer.org/sound_synthesis_introduction/Processed_Recording/table_lookup_oscillator/

# Table Lookup Oscillator | RingBuffer

**Source:** [ringbuffer.org](http://ringbuffer.org/sound_synthesis_introduction/Processed_Recording/table_lookup_oscillator/)

## Overview
Table-lookup oscillators are fundamental tools in digital signal processing (DSP) and audio synthesis, used to generate periodic waveforms (sine, square, triangle, sawtooth) and arbitrary waveforms.

### Core Trade-offs
| **Advantages** | **Drawbacks** |
| :--- | :--- |
| **Flexibility:** Different tables allow for the generation of any timbre. | **Distortion:** Small tables introduce quantization noise. |
| **Efficiency:** Precomputing waveforms minimizes runtime computation. | **Memory Usage:** Higher precision requires larger memory footprints. |

---

## The Generation Process
The method follows a three-step process to ensure smooth, efficient waveform generation.

### 1. The Phasor
A phasor acts as a cyclic pointer, typically ranging from $0$ to $2\pi$, which wraps around once it reaches its limit.

### 2. Phasor Scaling
The phasor is scaled to match the indices of the lookup table. To calculate the sample increment ($\Delta_s$) for a specific frequency ($f_0$) and sampling rate ($f_s$) with a table size of $N$:
$$\Delta_s = f_0 \frac{N}{f_s}$$

### 3. Table Lookup with Interpolation
To prevent "staircase" distortion (especially at high frequencies or low resolutions), interpolation is used to estimate values between table indices.

**Linear Interpolation Logic:**
1. **Identify indices:** 
   - Lower index: $p_l = \lfloor p \rfloor$
   - Upper index: $p_u = p_l + 1$
2. **Calculate fractional part:** $p_f = p - p_l$
3. **Compute interpolated value:**
$$T(p) = (1 - p_f) T[p_l] + p_f T[p_u]$$

---

## Performance & Efficiency
The primary advantage of lookup tables is the massive reduction in computational overhead compared to direct trigonometric functions.

### Computational Complexity
* **`sin()` Function:** $O(n)$ complexity (depends on required precision; uses Taylor series or CORDIC).
* **Lookup Table:** $O(1)$ complexity (simple array access and basic arithmetic).

### Speed Comparison
| Metric | `sin()` Function | Lookup Table |
| :--- | :--- | :--- |
| **Execution Time** | ~50–150 nanoseconds | ~1–5 nanoseconds |
| **Real-world Speedup** | Baseline | **10–100x improvement** |

**Example Case:** Generating a sine wave at 44.1 kHz for 1 second:
* **Using `sin()`:** ~4.41 ms total computation.
* **Using Lookup Table:** ~0.1 ms total computation (**~40x speedup**).

---

## Signal Quality: Distortion and SNR
The accuracy of the oscillator is directly tied to the table size ($N$) and the interpolation method.

### Quantization Noise
As the table size $N$ increases, quantization noise decreases logarithmically:
$$\text{Noise (dB)} \approx -6.02 \cdot \log_2(N) \quad \text{dB}$$

### Signal-to-Noise Ratio (SNR)
The SNR is influenced by the interpolation method used:

* **Without Interpolation:**
$$\text{SNR} \approx 6.02 \cdot \log_2(N) + 1.76 \quad \text{dB}$$

* **With Linear Interpolation:** (Provides a ~3 dB improvement)
$$\text{SNR}_{\text{linear}} = 6.02 \cdot \log_2(N) + 1.76 + 3 \quad \text{dB}$$

* **With Cubic Interpolation:** Offers even higher precision and SNR, though at a higher computational cost.

> **Key Insight:** Larger tables improve accuracy and increase SNR, while interpolation techniques (linear or cubic) mitigate the "rough" sound of low-resolution tables by smoothing the transitions between samples.

---
### Source: https://basicsynth.com/index.php?page=wvtables

# Wavetable Generators in Software Synthesis

Wavetable synthesis is a fundamental technique in software audio synthesis that utilizes a **memory-for-speed tradeoff**. Instead of calculating complex waveforms (like sine waves) using computationally expensive trigonometric functions in real-time, the waveform is pre-calculated and stored in a table.

## Core Principle: The Memory-Speed Tradeoff
By storing a single cycle of a periodic waveform in memory, the CPU only needs to perform a table lookup rather than a calculation. 

*   **Looping:** To create a continuous sound, the table index is incremented and then wrapped back to the beginning using a modulo operation (or manual boundary check) when it reaches the end of the table.
*   **Frequency Control:** Frequency is controlled by changing the **increment value** of the index. 
    *   An increment of `1` plays the table at its base frequency.
    *   An increment of `2` scans the table twice as fast, doubling the frequency.
*   **Implementation Logic:**
```c
frqTL = tableLength / sampleRate;
indexIncr = frqTL * frequency;
index = 0;
for (n = 0; n < totalSamples; n++) {
    sample[n] = wavetable[index];
    index += indexIncr;
    if (index >= tableLength)
        index -= tableLength;
}
```

## Advanced Wavetable Techniques
*   **Waveform Variety:** Tables are not limited to sine waves; they can contain summed sine waves, pre-computed waveforms loaded from files, or complex periodic shapes.
*   **Morphing:** "Morphing" is achieved by transitioning between different wavetables during sound generation.
*   **Frequency Modulation (FM):** FM is easily implemented by using a second wavetable (the modulator) to add its value to the index of the first wavetable (the carrier).
*   **Multi-period Tables:** A table can contain more than one period of a waveform, though the index increment must be adjusted (multiplied by the number of periods) to maintain the correct frequency.

## Interpolation and Error Reduction
Because the index increment is rarely an integer, the index will often point to a fractional position between two table entries. Simply truncating this to an integer introduces **quantization error**, which manifests as audible distortion.

### Linear Interpolation
To minimize distortion, **linear interpolation** can be used to estimate the value between two adjacent entries.

**Interpolation Code Snippet:**
```c
indexBase = floor(index);
indexFract = index - indexBase;
value1 = wavetable[indexBase];
value2 = wavetable[indexBase+1];
value = value1 + ((value2 - value1) * indexFract);
```

### Implementation Best Practices
1.  **The Boundary Trick:** To avoid expensive "end-of-table" checks during interpolation, allocate the table with a size of `tableLength + 1` and set the final element to the same value as `index 0`. This allows `indexBase + 1` to always be a valid lookup.
2.  **Table Size vs. Precision:**
    *   **Short Tables (≤ 4k):** Require interpolation to prevent significant distortion.
    *   **Long Tables (e.g., 16k):** The error from simple rounding is negligible. Increasing table length acts as a form of "pre-computed interpolation."
    *   **Efficiency:** For optimal performance, table lengths should always be a **power of two**.

## Summary Comparison
| Feature | Short Tables (e.g., 4k) | Long Tables (e.g., 16k) |
| :--- | :--- | :--- |
| **Computational Cost** | Higher (requires interpolation) | Lower (can use simple rounding) |
| **Audio Quality** | Potential for high distortion without interpolation | High fidelity with minimal error |
| **Memory Usage** | Low | Higher |

---
### Source: https://namoseley.wordpress.com/2015/07/26/sincos-generation-using-table-lookup-and-iterpolation/

# Sin/cos Generation via Table Lookup and Interpolation

This article explores efficient methods for generating sine and cosine waveforms for high-speed signal processing applications (such as Software Defined Radio or test equipment), where polynomial-based calculations are too computationally expensive.

## Core Concept: The Phase Accumulator
To generate a waveform, a table of $N$ entries is stored in memory. By reading the table at a constant frequency $F_s$, the resulting waveform frequency is $F = F_s/N$. 

The implementation uses a **fixed-point (16.16) phase accumulator**. The upper bits represent the integer index into the table, while the lower bits represent the fractional position between entries.

---

## Algorithm #1: Simple Table Lookup
The simplest method involves filling a table with a single cycle of a sine wave and reading entries by truncating the fractional bits of the phase accumulator.

**Formula:**
$$\text{table}[k] = \sin \left( 2 \cdot \pi \cdot \frac{k}{N} \right)$$

**Implementation Snippet:**
```c
/* Fixed-point (16.16) phase increment */
uint32_t phaseIncrement=(256*65536*desiredFreq/sampleRate);

while(true) 
{
    /* Increment the phase accumulator */
    phaseAccumulator += phaseIncrement;
    /* Limit the phase accumulator to 24 bits (8-bit index, 16-bit fraction) */
    phaseAccumulator &= (256*65536)-1; 
    
    /* Read the table by truncating the lower 16 bits */
    uint32_t tableIndex = phaseAccumulator >> 16;
    printf("%f\n", table[tableIndex]);
}
```

**Evaluation:**
* **Spectral Purity:** Poor.
* **Performance:** Significant spurious responses as high as **-50 dBc** due to the finite length of the table.

---

  ## Algorithm #2: Table Lookup and Linear Interpolation
To improve accuracy without significantly increasing table size, we use the discarded 16-bit fractional part to interpolate between two adjacent table entries.

**Implementation Details:**
* The table size is increased to $N+1$ (e.g., 257 entries) to ensure `table[index+1]` never causes an out-of-bounds read.
* **Formula:** $\text{out} = v_1 + (v_2 - v_1) \cdot \text{fractional\_part}$

**Implementation Snippet:**
```c
/* ... inside while loop ... */
uint32_t index = phaseAccumulator >> 16;
float v1 = table[index];
float v2 = table[index+1];

/* Convert fixed-point fractional part to float */
float fmul = static_cast<float>(phaseAccumulator & 65535) / 65536.0f;

/* Perform linear interpolation */
float out = v1 + (v2 - v1) * fmul;
```

**Evaluation:**
* **Spectral Purity:** Significantly improved.
* **Performance:** Spurious responses are better than **-90 dBc**, which is sufficient for many software radio applications.

---

## Algorithm #3: Table Lookup and Circular Interpolation
For high-precision applications (like test equipment), circular interpolation uses trigonometric identities to achieve much higher purity.

**Mathematical Foundation:**
1. **Identity:** $\sin(A+B) = \sin(A) \cdot \cos(B) + \cos(A) \cdot \sin(B)$
2. **Lookup:** $A$ is derived from the upper 8 bits (integer part). $\sin(A)$ and $\cos(A)$ are retrieved from the table.
3. **Small Angle Approximation:** $B$ is the fractional angle (lower 16 bits). Since $B$ is small:
   * $\sin(B) \approx B$
   * $\cos(B) \approx 1 - \frac{B^2}{2}$
4. **Cosine Lookup:** $\cos(A)$ is found by looking up $\sin(A + \frac{\pi}{2})$, which is simply 64 entries ahead in a 256-entry table.

**Implementation Snippet:**
```c
/* ... inside while loop ... */
uint32_t index = phaseAccumulator >> 16;
float v_sin = table[index];
float v_cos = table[(index+64) & 255]; // Cosine is 90 degrees (64 entries) ahead
float frac = 2.0f * 3.1415927f * static_cast<float>(phaseAccumulator & 65535) / 65536.0f / 256.0f;

/* Fractional sin/cos approximations */
float f_sin = frac;
float f_cos = 1.0f - 0.5f * frac * frac;

/* Final result using the addition identity */
float result = v_sin * f_cos + v_cos * f_sin;
```

**Evaluation:**
* **Spectral Purity:** Excellent.
* **Performance:** Spurious responses drop to approximately **-128 dBc** (a 38 dB improvement over linear interpolation).
* **Scaling:** Increasing the table size to 512 entries can push spurious responses below **-147 dBc**.

---

## Summary Comparison

| Method | Complexity | Spectral Purity (Spurious Response) | Best Use Case |
| :--- | :--- | :--- | :--- |
| **Simple Lookup** | Very Low | ~ -50 dBc | Low-precision/High-speed |
| **Linear Interpolation** | Moderate | ~ -90 dBc | Software Defined Radio (SDR) |
| **Circular Interpolation** | Higher | ~ -128 dBc | Test Equipment / High-precision |

**Key Insight:** These algorithms are also ideal for generating **quadrature signals**, as both sine and cosine waveforms can be calculated simultaneously using the same phase accumulator.

---