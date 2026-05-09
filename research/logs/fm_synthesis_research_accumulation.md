     1|     1|     1|
     2|     2|     2|
     3|     3|     3|# FM Synthesis Research: Architecture & Complexity Findings
     4|     4|     4|
     5|     5|     5|### FM Architecture
     6|     6|     6|- **[FM synthesis explained: a frequency modulation cheat sheet for music producers - RouteNote Blog](https://routenote.com/blog/fm-synthesis-explained/)**: Today, frequency modulation relies on the following things to work:
     7|     7|     7|[...]
     8|     8|     8|1. Signal operators
     9|     9|     9|2. Algorithms
    10|    10|    10|3. Ratios
    11|    11|    11|[...]
    12|    12|    12|Operators are like oscillators, but they’re self-sufficient as they have their own dedicated amplitude envelopes.
    13|    13|    13|[...]
    14|    14|    14|Furthermore, FM synths utilize two different types of operators known as “carriers” and “modulators“. Traditionally, FM synth operators only house sine waveforms. But the FM synths of today include other waveforms!
    15|    15|    15|[...]
    16|    16|    16|Carrier operators contain the signal that a modulator operator modulates the frequency of with its own signal. In other words, FM synths route the output of carrier operators into modulator operators.
    17|    17|    17|[...]
    18|    18|    18|The Yamaha DX7 has six operators in total but the majority of FM synths generally have four operators today. Though this may seem like a limitation, the ability to use waveforms other than sine waves make it easier to build harmonically rich sounds.
    19|    19|    19|[...]
    20|    20|    20|As you may have guessed, FM synthesis requires two signals: one to modulate and one to be modulated.
    21|    21|    21|[...]
    22|    22|    22|Operators perform both of these functions. The modulator (a fitting name) houses the signal that will modulate the frequency of the carrier signal. Therefore, the modulator is like an LFO applied to the pitch of an oscillator signal.
    23|    23|    23|[...]
    24|    24|    24|Each operator has its own amplitude envelope and they work just like they do in other synthesizers like Serum, Massive, and Vital.
    25|    25|    25|[...]
    26|    26|    26|Both the modulator and carrier operators have ADSR envelopes, so you can shape the amplitude of each operator as you see fit – allowing for some pretty interesting timbres. In short, adjusting and modulating the amplitudes of each operator over time creates some pretty wacky frequency changes…
    27|    27|    27|[...]
    28|    28|    28|#### Algorithms in FM synthesis
    29|    29|    29|[...]
    30|    30|    30|How each operator is connected to others makes a massive difference to your sound. Yamaha realized that having 6 operators available on the DX7 may get overwhelming for users. So they created a fixed amount of operator arrangements for the most useful configurations making the DX7 far simpler to use.
    31|    31|    31|[...]
    32|    32|    32|These operator configurations are called “algorithms“. The bottom row of operators in any algorithm are carriers while the rest are modulators.
    33|    33|    33|[...]
    34|    34|    34|In example 5, both carriers have one operator modulating each of them while operator 4 has a feedback loop that consistently modulates its own frequency.
    35|    35|    35|[...]
    36|    36|    36|#### Ratios in FM synthesis
    37|    37|    37|[...]
    38|    38|    38|FM synths utilize “ratios” for adjusting the pitch of operators instead of the traditional scale of semitones and cents. In fact, Ratios make it easier to predict the kind of sound you’re going to get.
    39|    39|    39|[...]
    40|    40|    40|In practice, the modulator frequency will most often be a multiple of the carrier.
    41|    41|    41|[...]
    42|    42|    42|For example, a modulator frequency with a ratio of 1:1 means the modulator frequency is the same as the frequency of the carrier signal and produces only a little harmonic content. But a 2:1 ratio produces a more complex tone, and the higher you go gives more complex timbres.
    43|    43|    43|[...]
    44|    44|    44|Doubling the ratio is the same as increasing the pitch by an octave. So a 2:1 ratio give is an octave higher, a 4:1 ratio is an octave higher again, and so on. As a result, high ratios on your modulator signal are how you get complex harmonic sounds.
    45|    45|    45|- **[Step-by-step FM synthesis explanation](https://gist.github.com/jrmoserbaltimore/68049858f0ab5fee9eddcb6de663ddf2)**: # OPL3
    46|    46|    46|[...]
    47|    47|    47|Using the OPL3 as an example, there are several components:
    48|    48|    48|
    49|    49|    49|* Vibrato feeds into the phase generator (PG) to modulate its frequency
    50|    50|    50|* Tremolo feeds into the envelope generator (EG) to modulate its amplitude
    51|    51|    51|* PG and EG feed into the operator
    52|    52|    52|* Operator outputs to the next operator
    53|    53|    53|
    54|    54|    54|Within the operator, the input—either nothing or the prior FM operator's output—is combined with the PG signal and any feedback.  The result goes into a sine function and is multiplied by the EG's amplitude.
    55|    55|    55|[...]
    56|    56|    56|Yamaha writes this as `A×sin(ω`<sub>`c`</sub>`[t]+B×sin(ω`<sub>`m`</sub>`[t]))`, with `A` being the carrier amplitude, `ω`<sub>`c`</sub> being the angular frequency of the carrier (phase generator), `B` being the modulator amplitude, and `ω`<sub>`m`</sub> being the angular frequency of the modulator.  The modulator is the first operator, which feeds into the carrier:  the carrier produces a signal (carrier signal), modulating it by the signal provided by the modulator.
    57|    57|    57|[...]
    58|    58|    58|To understand FM synthesis, we'll just carry it out step by step for the OPL3.
    59|    59|    59|[...]
    60|    60|    60|## logsin and exponent tables
    61|    61|    61|[...]
    62|    62|    62|The OPL3 uses two look-up tables, each containing 256 entries.
    63|    63|    63|[...]
    64|    64|    64|The exponent table `exp[]` is calculated by `f(x)=round((power(2, x/256)-1)×1024)` for `x` from 0 to 255.  Given a value, the significand is calculated by exp[8 LSB] + 1024, and the exponent is the remaining MSBs, producing a floating-point value.  Ten bits of exponent are required to represent all values in the table, the largest being 1,018.
    65|    65|    65|[...]
    66|    66|    66|The logsin table `logsin[]` is calculated by `f(x)=round(-log(sin((x+0.5)*pi/512))/log(2)*256)` for `x` from 0 to 255.  It requires 12 bits to represent all values (largest is 2,137) and represents the first rising quarter of a sine wave.
    67|    67|    67|[...]
    68|    68|    68|The OPL3 provides 8 waveforms for the phase generator:
    69|    69|    69|[...]
    70|    70|    70|* Sine:  Sine[0:255] traversed forward/backward and inverted
    71|    71|    71|* Half-sine:  Sine[0:255], Sine[255:0], toggle output to be table or 0 each time index=0
    72|    72|    72|* Abs-sine:  Sine[0:255] is traversed forward/backward but not inverted
    73|    73|    73|* Pulse-sine:  lookup table is Sine[0:255], toggle between table and 0 at overflow
    74|    74|    74|* Even-Sine:  Sine[], toggle between table and 0 each full period
    75|    75|    75|* Even-abs-sine:  Even-sine without inversion
    76|    76|    76|* Square:  Literally just the sign of the sine wave.
    77|    77|    77|* Derived square: ?
    78|    78|    78|[...]
    79|    79|    79|The OPL3 decapsulation project says output is generated as `exp[logsin[phase2 + exp[logsin[phase1] + gain1]] + gain2]` which is mysterious and terrifying.  This is the equation `exp[logsin[ω`<sub>`c`</sub>` + exp[logsin[ω`<sub>`m`</sub>`] + B]] + A]`.
    80|    80|    80|
    81|    81|    81|How does that even work?
    82|    82|    82|[...]
    83|    83|    83|`A×sin(x)` is equivalent to `e`<sup>`(ln(a) + ln(sin(x)))`</sup>, hence the entry in `exp[]` is the addition of an entry in `logsin[]` and some figure by which the `logsin[]` entry is multiplied.
    84|    84|    84|
    85|    85|    85|This implies `B` and `A` must be log of the envelope.
    86|    86|    86|[...]
    87|    87|    87|So how do we pass from one operator to the next?
    88|    88|    88|
    89|    89|    89|`Op1out = exp[logsin[phase1 + Op1in] + gain1]`
    90|    90|    90|
    91|    91|    91|`Op2out = exp[logsin[phase2 + Op2in] + gain2]`
    92|    92|    92|[...]
    93|    93|    93|`Op1in` is silence or feedback; `Op2in` is silence or `Op1out`.  That makes the general formula `exp[logsin[ω + Input] + EG]]`, where `EG` is the log of the envelope (`log(A)`, note `logsin[A]` is `log(sin(A))` and is different).
    94|    94|    94|[...]
    95|    95|    95|I have no idea?  Need to
    96|    96|    96|[...]
    97|    97|    97|how the EG, PG, and vibrato come up with their exact values to get the
    98|    98|    98|[...]
    99|    99|    99|to the equations above.
   100|   100|   100|[...]
   101|   101|   101|> It's simple enough. A and B are the gain values (loudness), omega is the angular frequency (Hz), and c[t] and m[t] are the phase of the oscillator at the given time (as a function of time). The big part is the adding of the modulator's amplified output to the phase of the carrier. This is only FM when the modulator is a sine wave itself; this is technically phase modulation.
   102|   102|   102|> 
   103|   103|   103|> Look up the datasheet for the OPL3 and OPL4, they have a diagram of the FM carrier-operator relationship in there.
   104|   104|   104|- **[FM Theory And Applications](https://burnkit2600.com/manuals/fm_theory_and_applications.pdf)**: 4. ‘SIMPLE FM — The TROON ceccsecccsescsesseseeeenesreensasansaceesreseesssessnsneaaseseeeseneeeeeeae 29
   105|   105|   105|[...]
   106|   106|   106|5 SIMPLE FM — The Practice ........-seeccecssseeseesseeeetssesssannscsssssnenessennsnesenssannseeen 83
   107|   107|   107|[...]
   108|   108|   108|6 COMPLEX FM. ose eesccseeceeseeseennneesee sesessseeseeseecensssecessnecesneeesasveresanieensneeeenussanieess 113
   109|   109|   109|[...]
   110|   110|   110|APPENDIX 1 — Logarithmic Representation and “Pitch Frequency’”........0 160
   111|   111|   111|[...]
   112|   112|   112|APPENDIX 2 — “X” Synth Comparisons by Index vs. Op. Output Level.......... 165
   113|   113|   113|[...]
   114|   114|   114|APPENDIX 3 — Bessel FUNCTIONS GFaPhS.....csesecseesnteeaeeneteeteeteetnettneeneteneee 170
   115|   115|   115|[...]
   116|   116|   116|APPENDIX 4 — Bessel FUNCtION Table... ccc cssseeeetee sense ceeeeeesetereneeneceeteaenensasanes 179
   117|   117|   117|- **[Frequency modulation synthesis - Wikipedia](https://en.wikipedia.org/wiki/FM_synthesis)**: In 2016, Korg released the Korg Volca FM, a, 3-voice, 6 operators FM iteration of the Korg Volca series of compact, affordable desktop modules.[18] Korg has also released the opsix(2020) and opsix SE (2023), integrating 6 operators FM synthesis with subtractive, analogue modeling, additive, semi-modular and Waveshaping.
   118|   118|   118|[...]
   119|   119|   119|201
   120|   120|   120|[...]
   121|   121|   121|, which combines a 128-voice sample-based engine with a 12
   122|   122|   122|[...]
   123|   123|   123|FM-X
   124|   124|   124|[...]
   125|   125|   125|and features 8 operators;
   126|   126|   126|[...]
   127|   127|   127|wave forms, but each wave form has several parameters to adjust
   128|   128|   128|[...]
   129|   129|   129|20
   130|   130|   130|[...]
   131|   131|   131|with 64-voice,
   132|   132|   132|[...]
   133|   133|   133|8 operators FM-X architecture
   134|   134|   134|[...]
   135|   135|   135|128-
   136|   136|   136|[...]
   137|   137|   137|sample-based engine
   138|   138|   138|[...]
   139|   139|   139|of the FM
   140|   140|   140|[...]
   141|   141|   141|same as with
   142|   142|   142|[...]
   143|   143|   143|was succeeded by
   144|   144|   144|[...]
   145|   145|   145|Montage M in
   146|   146|   146|[...]
   147|   147|   147|uses the same 1
   148|   148|   148|[...]
   149|   149|   149|8 operators FM
   150|   150|   150|[...]
   151|   151|   151|16-
   152|   152|   152|[...]
   153|   153|   153|-X.[
   154|   154|   154|[...]
   155|   155|   155|FM-X synthesis was first introduced with the Yamaha Montage synthesizers in 2016. FM-X uses 8 operators. Each FM-X operator has a set of multi-spectral wave forms to choose from, which means each FM-X operator can be equivalent to a stack of 3 or 4 DX7 FM operators. The list of selectable wave forms includes sine waves, the All1 and All2 wave forms, the Odd1 and Odd2 wave forms, and the Res1 and Res2 wave forms. The sine wave selection works the same as the DX7 wave forms. The All1 and All2 wave forms are a saw-tooth wave form. The Odd1 and Odd2 wave forms are pulse or square waves. These two types of wave forms can be used to model the basic harmonic peaks in the bottom of the harmonic spectrum of most instruments. The Res1 and
   156|   156|   156|[...]
   157|   157|   157|2 wave forms move the spectral peak to a specific harmonic and can be used to model either triangular or rounded groups of
   158|   158|   158|[...]
   159|   159|   159|further up in
   160|   160|   160|[...]
   161|   161|   161|spectrum of an instrument. Combining an All1 or Odd1 wave form with multiple Res1 (or Res2) wave forms (and adjusting their amplitudes) can
   162|   162|   162|[...]
   163|   163|   163|The spectrum generated by FM synthesis with one modulator is expressed as follows:[24][25]
   164|   164|   164|[...]
   165|   165|   165|For modulation signal m ( t ) = B sin ⁡ ( ω m t ) {\displaystyle m(t)=B\,\sin(\omega _{m}t)\,}, the carrier signal is:[note 1]
   166|   166|   166|[...]
   167|   167|   167|F M ( t ) = A sin ⁡ ( ∫ 0 t ( ω c + B sin ⁡ ( ω m τ ) ) d τ ) = A sin ⁡ ( ω c t − B ω m ( cos ⁡ ( ω m t ) − 1 ) ) = A sin ⁡ ( ω c t + B ω m ( sin ⁡ ( ω m t − π / 2 ) + 1 ) ) {\displaystyle {\begin{aligned}FM(t)&\ =\ A\,\sin \left(\,\int _{0}^{t}\left(\omega _{c}+B\,\sin(\omega _{m}\,\tau )\right)d\tau \right)\\&\ =\ A\,\sin \left(\omega _{c}\,t-{\frac {B}{\omega _{m}}}\left(\cos(\omega _{m}\,t)-1\right)\right)\\&\ =\ A\,\sin \left(\omega _{c}\,t+{\frac {B}{\omega _{m}}}\left(\sin(\omega _{m}\,t-\pi /2)+1\right)\right)\\\end{aligned}}}
   168|   168|   168|[...]
   169|   169|   169|If we were to ignore the constant phase terms on the carrier ϕ c = B / ω m {\displaystyle \phi _{c}=B/\omega _{m}\,} and the modulator ϕ m = − π / 2 {\displaystyle \phi _{m}=-\pi /2\,}, finally we would get the following expression, as seen on Chowning 1973 and Roads 1996, p. 232:
   170|   170|   170|[...]
   171|   171|   171|F M ( t ) ≈ A sin ⁡ ( ω c t + β sin ⁡ ( ω m t ) ) = A ( J 0 ( β ) sin ⁡ ( ω c t ) + ∑ n = 1 ∞ J n ( β ) [ sin ⁡ ( ( ω c + n ω m ) t ) + ( − 1 ) n sin ⁡ ( ( ω c − n ω m ) t ) ] ) = A ∑ n = − ∞ ∞ J n ( β ) sin ⁡ ( ( ω c + n ω m ) t ) {\displaystyle {\begin{aligned}FM(t)&\ \approx \ A\,\sin \left(\omega _{c}\,t+\beta \,\sin(\omega _{m}\,t)\right)\\&\ =\ A\left(J_{0}(\beta )\sin(\omega _{c}\,t)+\sum _{n=1}^{\infty }J_{n}(\beta )\left[\,\sin((\omega _{c}+n\,\omega _{m})\,t)\ +\ (-1)^{n}\sin((\omega _{c}-n\,\omega _{m})\,t)\,\right]\right)\\&\ =\ A\sum _{n=-\infty }^{\infty }J_{n}(\beta )\,\sin((\omega _{c}+n\,\omega _{m})\,t)\end{aligned}}}
   172|   172|   172|[...]
   173|   173|   173|where ω c , ω m {\displaystyle \omega _{c}\,,\,\omega _{m}\,} are angular frequencies( ω = 2 π f {\displaystyle \,\omega =2\pi f\,}) of carrier and modulator, β = B / ω m {\displaystyle \beta =B/\omega _{m}\,} is frequency modulation index, and amplitudes J n ( β ) {\displaystyle J_{n}(\beta )\,} is n {\displaystyle n\,}-th Bessel function of first kind, respectively.[note 2]
   174|   174|   174|- **[Dmfm.html](http://www.4front-tech.com/dmguide/dmfm.html)**: modulation to perform
   175|   175|   175|[...]
   176|   176|   176|synthesizer supports O
   177|   177|   177|[...]
   178|   178|   178|3 mode (2 or 4
   179|   179|   179|[...]
   180|   180|   180|Adlib mono mode).
   181|   181|   181|[...]
   182|   182|   182|- OPL-3 mode supports 18 channels of 2-operator Stereo FM tones or 6 channels of 4-operator and 6 channels of 2-operator FM tones 5 percussion instrument channels
   183|   183|   183|- OPL-2 mode (Adlib) supports 9 channels of 2-operator FM tones or 6 channels of 2 operator FM tones + 5 percussion instruments
   184|   184|   184|- Adlib Compatible OPL-2 mode
   185|   185|   185|- Yamaha YMF 262 OPL-3 Chip
   186|   186|   186|[...]
   187|   187|   187|FM synthesis uses a modulator cell and a carrier cell. The modulator cell modulates the carrier cell.The FM synthesizer provides 36 cells comprising of 18 modulator cells and l8 carrier cells that result in 18 simultaneous channels being generated. In essence, the 18 channels can generate any 2 operator note.
   188|   188|   188|[...]
   189|   189|   189|There are basically 2 modes supported by the FM synthesizer: OPL-2 and OPL-3 modes. OPL-2 mode consists of nine 2-operator note channels with mono output. OPL-3 mode consists of eighteen 2-operator note channels with stereo output or six 4-operator and six 2-operator channels. Both OPL-2 and OPL-3 modes support 5 percussion instruments and when the rhythm mode is selected, the percussion instruments occupy 3 channels (l channel for bass drum, l/2 channel for the other 4 percussion instruments).
   190|   190|   190|[...]
   191|   191|   191|```
   192|   192|   192|struct dm_fm_voice 
   193|   193|   193|{
   194|   194|   194|     unsigned char op;          /*0 for modulator and 1 for carrier */
   195|   195|   195|     unsigned char voice;       /*Channels of 2-op notes*/
   196|   196|   196|     unsigned char am;          /*Tremolo or AM modulation effect flag
   197|   197|   197|[...]
   198|   198|   198|1 bit*/
   199|   199|   199|     unsigned char vibrato;     /*Vibr
   200|   200|   200|[...]
   201|   201|   201|bit*/
   202|   202|   202|     unsigned char do_s
   203|   203|   203|[...]
   204|   204|   204|;  /*
   205|   205|   205|[...]
   206|   206|   206|unsigned char k
   207|   207|   207|[...]
   208|   208|   208|_scale;
   209|   209|   209|[...]
   210|   210|   210|;    /*
   211|   211|   211|[...]
   212|   212|   212|- voice - holds the voice or the channel number. There are 36 op cells that result in 18 channels that can produce a note simultaneously. A value of 0 through 17 specify a channel. In rhythm mode
   213|   213|   213|[...]
   214|   214|   214|channels 6
   215|   215|   215|[...]
   216|   216|   216|be used to generate
   217|   217|   217|[...]
   218|   218|   218|that result in
   219|   219|   219|[...]
   220|   220|   220|- FM_IOCTL_SET_MODE Parameters: OPL2 or OPL3 This ioctl is used to set the mode of the FM synthesizer. The parameters for this ioctl are OPL2 or OPL3. OPL2 sets the FM synthesizer in OPL2 or ADLIB compatible mode. In this mode only 9 channels of 2 op voices with mono output are permitted. In OPL3 mode, there are 18 channels of 2 op with stereo output or 6 channels of 4 ops and 6 channels of 2 ops with stereo output. The ioctl to set the FM synthesizer in OPL3 mode is as follows: ioctl(fmfd, FM_IOCTL_SET_MODE, OPL3);
   221|   221|   221|[...]
   222|   222|   222|- FM_IOCTL_SET_OPL Paramters: (char) conn_type; This ioctl is used to set the connection type for the 4 op mode. The parameter conn_type is a byte defining the connection. If you want the synthesizer in 2 -op mode then the conn_type = 0x0. If you want the synthesizer in 4-op mode with six 4-op channels then conn_type = 0x3F.
   223|   223|   223|[...]
   224|   224|   224|### FM Synthesizer in 4 - Operator Mode
   225|   225|   225|[...]
   226|   226|   226|In th 4-op mode, the FM synthesizer uses 4 ops which actually comprize of two 2-op channels. From 18 2-op channels, we can get six 4-op channels, with 5 channels for percussion (as described above) and three 2-op channels used for FM voices. The diagram below describes how 18 2-op channels are organized:
   227|   227|   227|[...]
   228|   228|   228|The ioctl FM_IOCTL_SET_OPL is used to set the 4-op connection mask. This ioctl requires a 6 bit mask. If the mask is 0 then all the FM voice channels default to 2 op mode thus yielding 18 channels or 15 voice plus 5 percussion channels. If the mask is set to 0x3F then the FM synthesizer is configured for six 4 op voice channels plus six 2 op voice channels. The six 2 op voice channels can be configured for 5 percussion channels a 3 voice channels or 6 voice channels. In the case of the 4 -op channels the above diagram shows which two op channels go into building the six 4 op channels.
   229|   229|   229|[...]
   230|   230|   230|With 4 ops, the following diagram shows how the ops can be connected. The connection bits from the first two ops is designated as C0 and the connection bits from the remaining two are designated as C1.
   231|   231|   231|
   232|   232|   232|### PM vs FM
   233|   233|   233|- **[Phase modulation Vs. Frequency modulation II — Musicdsp.org  documentation](https://www.musicdsp.org/en/latest/Synthesis/160-phase-modulation-vs-frequency-modulation-ii.html)**: The difference between FM & PM in a digital oscillator is that FM is added to the
   234|   234|   234|frequency before the phase integration, while PM is added to the phase after the phase
   235|   235|   235|integration. Phase integration is when the old phase for the oscillator is added to the
   236|   236|   236|current frequency (in radians per sample) to get the new phase for the oscillator. The
   237|   237|   237|equivalent PM modulator to obtain the same waveform as FM is the integral of the FM
   238|   238|   238|modulator. Since the integral of sine waves are inverted cosine waves this is no problem.
   239|   239|   239|[...]
   240|   240|   240|In modulators with multiple partials, the equivalent PM modulator will have different
   241|   241|   241|relative partial amplitudes. For example, the integral of a square wave is a triangle
   242|   242|   242|wave; they have the same harmonic content, but the relative partial amplitudes are
   243|   243|   243|different. These differences make no difference since we are not trying to exactly
   244|   244|   244|recreate FM, but real (or nonreal) instruments.
   245|   245|   245|[...]
   246|   246|   246|The reason PM is better is because in PM and FM there can be non-zero energy produced at 0
   247|   247|   247|Hz, which in FM will produce a shift in pitch if the FM wave is used again as a modulator,
   248|   248|   248|however in PM the DC component will only produce a phase shift. Another reason PM is
   249|   249|   249|better is that the modulation index (which determines the number of sidebands produced and
   250|   250|   250|which in normal FM is calculated as the modulator amplitude divided by frequency of
   251|   251|   251|modulator) is not dependant on the frequency of the modulator, it is always equal to the
   252|   252|   252|amplitude of the modulator in radians. The benefit of solving the DC frequency shift
   253|   253|   253|problem, is that cascaded carrier-modulator pairs and feedback modulation are possible.
   254|   254|   254|[...]
   255|   255|   255|The simpler calculation of modulation index makes it easier to have voices keep the same
   256|   256|   256|harmonic structure throughout all pitches.
   257|   257|   257|[...]
   258|   258|   258|Below is some C code for a digital oscillator that implements FM,PM,and AM. It illustrates
   259|   259|   259|the difference in implementation of FM & PM. It is only meant as an example, and not as an
   260|   260|   260|efficient implementation.
   261|   261|   261|[...]
   262|   262|   262|| 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 | /* Example implementation of digital oscillator with FM, PM, & AM */ #define PI 3.14159265358979 #define RADIANS_TO_INDEX (512.0 / (2.0 * PI)) typedef struct{ /* oscillator data */ double freq; /* oscillator frequency in radians per sample */ double phase; /* accumulated oscillator phase in radians */ double wavetable[512]; /* waveform lookup table */ } OscilRec; /* oscil - compute 1 sample of oscillator output whose freq. phase and * wavetable are in the OscilRec structure pointed to by orec. */ double oscil(orec, fm, pm, am) OscilRec *orec; /* pointer to the oscil's data */ double fm; /* frequency modulation input in radians per sample */ double pm; /* phase modulation input in radians */ double am; /* amplitude modulation input in any units you want */ { long tableindex; /* index into wavetable */ double instantaneous_freq; /* oscillator freq + freq modulation */ double instantaneous_phase; /* oscillator phase + phase modulation */ double output; /* oscillator output */ instantaneous_freq = orec->freq + fm; /* get instantaneous freq */ orec->phase += instantaneous_freq; /* accumulate phase */ instantaneous_phase = orec->phase + pm; /* get instantaneous phase */ /* convert to lookup table index */ tableindex = RADIANS_TO_INDEX * instantaneous_phase; tableindex &= 511; /* make it mod 512 === eliminate multiples of 2*k*PI */ output = orec->wavetable[tableindex] * am; /* lookup and mult by am input */ return (output); /* return oscillator output */ } |
   263|   263|   263|[...]
   264|   264|   264|As the PM/FM is "iterative", won't this code produce different results at different sampling rates? How can this be prevented?
   265|   265|   265|- **[An Introduction to FM](https://ccrma.stanford.edu/software/clm/clm/cl-fm.html)**: In PM we change the phase, in FM we change the phase increment, and to go from FM to PM, integrate the FM modulating signal. But you can't tell which is in use from the output waveform; you have to know what the modulating signal is. In sound synthesis, where we can do what we want with the modulating signal, there is no essential difference between frequency and phase modulation.
   266|   266|   266|[...]
   267|   267|   267|I would call this issue a dead horse, but it is still causing confusion, even 40 years down the road. So, here are two CLM instruments, one performing phase modulation, the other performing frequency modulation. I have tried to make the innards explicit at each step, and match the indices so that the instruments produce the same results given the same parameters. Also, to lay a different controversy to rest, it should be obvious from these two functions that there is no difference in run-time computational expense or accuracy.
   268|   268|   268|[...]
   269|   269|   269|-ratio" = modulator to
   270|   270|   270|[...]
   271|   271|   271|((carrier-phase
   272|   272|   272|[...]
   273|   273|   273|.0) ; set to pi/2 if someone tells you PM can't generate energy at DC
   274|   274|   274|        (carrier-phase-incr (hz->radians freq
   275|   275|   275|[...]
   276|   276|   276|modulator-phase
   277|   277|   277|[...]
   278|   278|   278|)
   279|   279|   279|        (modulator-phase-incr (hz->radians (* freq mc-ratio
   280|   280|   280|[...]
   281|   281|   281|)
   282|   282|   282|        (val 0.0))
   283|   283|   283|   (run
   284|   284|   284|     (loop for i from beg to end do
   285|   285|   285|       (setf modulation (* index (sin modulator-phase)))
   286|   286|   286|       (setf val (* amp (sin (+ carrier-phase modulation)))) 
   287|   287|   287|       ;; no
   288|   288|   288|[...]
   289|   289|   289|in phase modulation
   290|   290|   290|       (incf carrier-phase carrier-phase-incr)
   291|   291|   291|       (incf modulator-phase modulator-phase-incr)
   292|   292|   292|       (outa i val)))))
   293|   293|   293|[...]
   294|   294|   294|(definstrument fm (beg end freq amp mc-ratio index)
   295|   295|   295|  (let* ((carrier-phase 0.0)
   296|   296|   296|         (carrier-phase-incr (hz->radians freq))
   297|   297|   297|         (modulator-phase-incr (hz->radians (* freq mc-ratio)))
   298|   298|   298|         (modulator-phase (* 0.5 (+ pi modulator-phase-incr)))
   299|   299|   299|	 ;; (pi+incr)/2 to get (centered) sin after integration, to match pm case above
   300|   300|   300|         (fm-index (hz->radians (* freq mc-ratio index)))  
   301|   301|   301|	 ;; fix up fm index (it's a frequency change)
   302|   302|   302|         (val 0.0)
   303|   303|   303|         (modulation 0.0))
   304|   304|   304|   (run
   305|   305|   305|     (loop for i from beg to end do
   306|   306|   306|       (setf modulation (* fm-index (sin modulator-phase)))
   307|   307|   307|       (incf carrier-phase modulation)      
   308|   308|   308|       ;; here is the fm integration
   309|   309|   309|       (setf val (* amp (sin carrier-phase)))
   310|   310|   310|       (incf carrier-phase carrier-phase-incr)
   311|   311|   311|       (incf modulator-phase modulator-phase-incr)
   312|   312|   312|       (outa i val)))))
   313|   313|   313|[...]
   314|   314|   314|10000 1000 .5 0
   315|   315|   315|[...]
   316|   316|   316|25 4))
   317|   317|   317|[...]
   318|   318|   318|slight difference! We
   319|   319|   319|[...]
   320|   320|   320|re using phase-modulation for simplicity (the integration
   321|   321|   321|[...]
   322|   322|   322|changes the effective initial phase). By
   323|   323|   323|[...]
   324|   324|   324|can get a changing spectrum from these cancellations. Here is a CLM
   325|   325|   325|[...]
   326|   326|   326|shows this (subtle) effect
   327|   327|   327|[...]
   328|   328|   328|In the second case we just add together the two simple FM spectra, but in the first case we get a more complex mixture involving all the sums and differences of the modulating frequencies. These sum and difference tones ("intermodulation products") are not limited to FM. Any nonlinear synthesis technique produces them. Being non-linear, it must have something that involves a power of its input other than 0 or 1; if we feed in sin a + sin b, for example, that term will produce not just (sin a)^n and (sin b)^n, but all sorts of stuff involving sin a * sin b (in various powers), and this produces things like cos(a+b) and cos(a-b). For a less impressionistic derivation of the spectrum, see Le Brun, "A Derivation of the Spectrum of FM with a Complex Modulating Wave". The result can be expressed:
   329|   329|   329|[...]
   330|   330|   330|You may have noticed that this is one case where phase modulation is different from FM. Previously, we could fix up each modulating sinusoid (both in amplitude and initial phase), but here we have no such handles on the components of the incoming signal. If someone insists, we can still match outputs by integrating the modulating signal: FM(white-noise) = PM(brownian-noise). Similarly, FM(square-wave) = PM(triangle-wave), FM(nxy1sin) = PM(square-wave), and FM(e^x) = PM(e^x). FM(square-wave) is:
   331|   331|   331|- **[Frequency and phase modulation](https://msp.ucsd.edu/techniques/latest/book-html/node79.html)**: If a sinusoid is given a frequency which varies slowly in time we hear it as having a varying pitch. But if the pitch changes so quickly that our ears can't track the change--for instance, if the change itself occurs at or above the fundamental frequency of the sinusoid--we hear a timbral change. The timbres so generated are rich and widely varying. The discovery by John Chowning of this possibility [Cho73] revolutionized the field of computer music. Here we develop frequency modulation, usually called FM, as a special case of waveshaping [Leb79] [DJ85, pp.155-158]; the analysis given here is somewhat different [Puc01].
   332|   332|   332|[...]
   333|   333|   333|The FM technique, in its simplest form, is shown in Figure 5.8 (part a). A frequency-modulated sinusoid is one whose frequency varies sinusoidally, at some angular frequency, about a central frequency, so that the instantaneous frequencies vary between and, with parameters controlling the frequency of variation, and controlling the depth of variation. The parameters,, and are called the carrier frequency, the modulation frequency, and the index of modulation, respectively.
   334|   334|   334|[...]
   335|   335|   335|It is customary to use a simpler, essentially equivalent formulation in which the phase, instead of the frequency, of the carrier sinusoid is modulated sinusoidally. (This gives an equivalent result since the instantaneous frequency is the rate of change of phase, and since the rate of change of a sinusoid is just another sinusoid.) The phase modulation formulation is shown in part (b) of the figure.
   336|   336|   336|[...]
   337|   337|   337|We can analyze the result of phase modulation as follows, assuming that the modulating oscillator and the wavetable are both sinusoidal, and that the carrier and modulation frequencies don't themselves vary in time. The resulting signal can then be written as
   338|   338|   338|[...]
   339|   339|   339|The parameter, which takes the place of the earlier parameter, is likewise called the index of modulation; it too controls the extent of frequency variation relative to the carrier frequency. If, there is no frequency variation and the expression reduces to the unmodified, carrier sinusoid; as increases the waveform becomes more complex.
   340|   340|   340|[...]
   341|   341|   341|| Figure 5.8: Block diagram for frequency modulation (FM) synthesis: (a) the classic form; (b) realized as phase modulation. |
   342|   342|   342|| --- |
   343|   343|   343|[...]
   344|   344|   344|To analyse the resulting spectrum we can rewrite the signal as,
   345|   345|   345|[...]
   346|   346|   346|We can consider the result as a sum of two waveshaping generators, each operating on a sinusoid of frequency and with a waveshaping index, and each ring modulated with a sinusoid of frequency. The waveshaping function is given by for the first term and by for the second.
   347|   347|   347|[...]
   348|   348|   348|Returning to Figure 5.4, we can predict what the spectrum will look like. The two harmonic spectra, of the waveshaping outputs
   349|   349|   349|[...]
   350|   350|   350|and each is multiplied by a sinusoid at the carrier frequency. So there will be a spectrum centered at the carrier frequency, with sidebands at both even and odd multiples of the modulation frequency, contributed respectively by the sine and cosine waveshaping terms above. The index of modulation, as it changes, controls the relative strength of the various partials. The partials themselves are situated at the frequencies
   351|   351|   351|[...]
   352|   352|   352|As with any situation where two periodic signals are multiplied, if there is some common supermultiple of the two periods, the resulting product will repeat at that longer period. So if the two periods are and, where and are relatively prime, they both repeat after a time interval of. In other words, if the two have frequencies which are both multiples of some common frequency, so that and, again with and relatively prime, the result will repeat at a frequency of the common submultiple. On the other hand, if no common submultiple can be found, or if the only submultiples are lower than any discernible pitch, then the result will be inharmonic.
   353|   353|   353|- **[An Introduction to FM](https://ccrma.stanford.edu/courses/tu/cm2008/topics/frequency_modulation/materials/snd/fm.html)**: In PM we change the phase, in FM we change the phase increment, and to go from FM to PM, integrate the FM modulating signal. But you can't tell which is in use from the output waveform; you have to know what the modulating signal is. In sound synthesis, where we can do what we want with the modulating signal, there is no essential difference between frequency and phase modulation.
   354|   354|   354|[...]
   355|   355|   355|I would call this issue a dead horse, but it is still causing confusion, even 40 years down the road. So, here are two CLM instruments, one performing phase modulation, the other performing frequency modulation. I have tried to make the innards explicit at each step, and match the indices so that the instruments produce the same results given the same parameters. Also, to lay a different controversy to rest, it should be obvious from these two functions that there is no difference in run-time computational expense or accuracy.
   356|   356|   356|[...]
   357|   357|   357|0) ; set to pi/2 if someone tells you PM can't
   358|   358|   358|[...]
   359|   359|   359|0Hz
   360|   360|   360|[...]
   361|   361|   361|(modulator-phase
   362|   362|   362|[...]
   363|   363|   363|.0)
   364|   364|   364|        (modulator-phase-incr (hz->radians (* mc-ratio freq))))
   365|   365|   365|    (
   366|   366|   366|[...]
   367|   367|   367|((= i end))
   368|   368|   368|      (let* ((modulation (* index (sin modulator-phase)))
   369|   369|   369|	     (pm-val (* amp (sin (+ carrier-phase modulation))))) 
   370|   370|   370|	     ;; no integration in phase modulation
   371|   371|   371|	(set! carrier-phase (+ carrier-phase carrier-phase-incr))
   372|   372|   372|	(set! modulator-phase (+ modulator-phase modulator-phase-incr))
   373|   373|   373|	(outa i pm-val)))))
   374|   374|   374|[...]
   375|   375|   375|define (fm beg end freq amp mc-ratio index)
   376|   376|   376|[...]
   377|   377|   377|let* ((carrier-phase
   378|   378|   378|[...]
   379|   379|   379|.0)
   380|   380|   380|	 (carrier-phase-incr (hz->radians freq))
   381|   381|   381|	 (modulator-phase-incr (hz->
   382|   382|   382|[...]
   383|   383|   383|ratio freq)))
   384|   384|   384|	 (modulator-phase (*
   385|   385|   385|[...]
   386|   386|   386|0.5 (+ pi modulator-phase-incr)))
   387|   387|   387|	 ;; (pi+incr)/2 to
   388|   388|   388|[...]
   389|   389|   389|(centered) sin after integration, to match pm case above
   390|   390|   390|[...]
   391|   391|   391|fm-index
   392|   392|   392|[...]
   393|   393|   393|ratio freq index))))
   394|   394|   394|	 ;; fix up fm index (
   395|   395|   395|[...]
   396|   396|   396|s a frequency change)
   397|   397|   397|[...]
   398|   398|   398|)))
   399|   399|   399|[...]
   400|   400|   400|end))
   401|   401|   401|[...]
   402|   402|   402|-index (sin modulator-phase)))
   403|   403|   403|[...]
   404|   404|   404|-val (* amp (sin carrier-phase))))
   405|   405|   405|[...]
   406|   406|   406|phase (+ carrier-phase modulation carrier-phase-incr))
   407|   407|   407|[...]
   408|   408|   408|There is a slight difference! We're using phase-modulation for simplicity (the integration in FM changes the effective initial phase). By varying the relative phases, we can get a changing spectrum from these cancellations. Here is a CLM instrument that shows this (subtle) effect:
   409|   409|   409|[...]
   410|   410|   410|In the second case we just add together the two simple FM spectra, but in the first case we get a more complex mixture involving all the sums and differences of the modulating frequencies. These sum and difference tones ("intermodulation products") are not limited to FM. Any nonlinear synthesis technique produces them. Being non-linear, it must have something that involves a power of its input other than 0 or 1; if we feed in sin a + sin b, for example, that term will produce not just (sin a)^n and (sin b)^n, but all sorts of stuff involving sin a * sin b (in various powers), and this produces things like cos(a+b) and cos(a-b). For a less impressionistic derivation of the spectrum, see Le Brun, "A Derivation of the Spectrum of FM with a Complex Modulating Wave". The result can be expressed:
   411|   411|   411|[...]
   412|   412|   412|side band amplitudes — see the immortal classic: Schottstaedt, "The Simulation of Natural Instrument Tones
   413|   413|   413|[...]
   414|   414|   414|a Complex Modulating Wave". (There's a function to do
   415|   415|   415|[...]
   416|   416|   416|for you in dsp.scm: fm-parallel-component). In simple cases, the
   417|   417|   417|[...]
   418|   418|   418|spectrum somewhat (see ncos for a discussion of a very different
   419|   419|   419|[...]
   420|   420|   420|-simple case). In general:
   421|   421|   421|[...]
   422|   422|   422|You may have noticed that this is one case where phase modulation is different from FM. Previously, we could fix up each modulating sinusoid (both in amplitude and initial phase), but here we have no such handles on the components of the incoming signal. If someone insists, we can still match outputs by integrating the modulating signal: FM(white-noise) = PM(brownian-noise). Similarly, FM(square-wave) = PM(triangle-wave), FM(nxy1sin) = PM(square-wave), and FM(e^x) = PM(e^x). FM(square-wave) is.
   423|   423|   423|- **[Frequency Modulation (FM) Synthesis](https://ccrma.stanford.edu/%7Ejos/sasp/Frequency_Modulation_FM_Synthesis.html)**: The first commercial digital sound synthesis method was Frequency Modulation(FM) synthesis [38, 41, 39], invented by John Chowning, the founding director of CCRMA. FM synthesis was discovered and initially developed in the 1970s [38]. The technology was commercialized by Yamaha Corporation, resulting in the DX-7(1983), the first commercial digital music synthesizer, and the OPL chipset, initially in the SoundBlaster PC sound card, and later a standard chipset required for ``SoundBlaster compatibility'' in computer multimedia support. The original pioneer patent expired in 1996, but additional patents were filed later. It is said that this technology lives on in cell-phone ring-tone synthesis.
   424|   424|   424|[...]
   425|   425|   425|As discussed more fully in [264, p. 44], the formula for elementary FM synthesis is given by
   426|   426|   426|[...]
   427|   427|   427|\begin{equation}x(t) = A_c\sin[\omega_c t + \phi_c + A_m\sin(\omega_m t + \phi_m)] \elabel{fm}\end{equation}
   428|   428|   428|[...]
   429|   429|   429|where \((A_c,\omega_c,\phi_c)\) specify the carrier sinusoid\((A_m,\omega_m,\phi_m)\) specify the modulator sinusoid An example computer-music-style diagram is shown in Fig.G.6. Since the instantaneous frequency of a sinusoid is simply the time-derivative of its instantaneous phase, FM can also be regarded as phase modulation(PM). It is highly remarkable that such a simple algorithm can generate such a rich variety of musically useful sounds. This is probably best understood by thinking of FM as a spectral modeling technique, as will be illustrated further below.
   430|   430|   430|
   431|   431|   431|### Wavtable vs Trig
   432|   432|   432|- **[Dannenberg, Roger, rbd@cs](https://www.cs.cmu.edu/~rbd/papers/tlu98/tlu.html)**: Waveform tables are an important tool for synthesizing sound, but they introduce error which results in noise. Error is affected by the spectrum of the signal stored in the table. Error is reduced by increasing the table size and/or by increasing the quality of interpolation. Both of these also affect the signal computation cost. Table sizes required for a given signal-to-noise ratio are computed for different interpolation methods and spectral rolloffs. Execution times are then evaluated. Non-interpolated oscillators perform the best, but only if the storage and computation costs of the tables are not an issue. This and other tradeoffs are discussed.
   433|   433|   433|[...]
   434|   434|   434|Linear interpolation is not the only option. In theory, any sampled signal can be recovered with arbitrarily low error provided that the signal’s sample rate is higher than twice its highest frequency component. In the case of tables, the table should have at least twice as many samples as harmonics. In practice, "proper" interpolation requires many expensive
   435|   435|   435|[...]
   436|   436|   436|operations and as many arithmetic operations, but even a short,
   437|   437|   437|[...]
   438|   438|   438|than linear interpolation. A
   439|   439|   439|[...]
   440|   440|   440|less accurate) technique is
   441|   441|   441|[...]
   442|   442|   442|Using higher-order interpolation requires more processing power. Table 1 shows the amount of processing time required per sample to compute one sample using different interpolation techniques. These measurements were made using a single oscillator with a table size of 512. Notice that the cost of interpolation is not proportional to the number of arithmetic operations. This is because arithmetic operations are only a part of the computation, and parallelism in the CPU allows some of these operations to overlap.
   443|   443|   443|[...]
   444|   444|   444|Table 2 shows computation time per sample for 16 tables. The table sizes were arbitrarily chosen to give 72 dB SNR with a 12 dB/octave rolloff using the various interpolation techniques. These results should be highly dependent upon cache size, machine architecture, and table size, which in turn depends upon the spectral rolloff and the desired SNR. In this particular experiment, performance decreased when multiple tables were used, but using even more tables did not further increase the per-sample execution time.
   445|   445|   445|[...]
   446|   446|   446|In all cases the non-interpolated oscillator is fastest. Notice, however, that when table sizes are chosen to give results with equal SNR, the relative advantage of non-interpolated oscillators is reduced. For the particular parameters chosen for this experiment, the non-interpolated oscillator is only about 1.55 times as fast as the quadratic interpolation oscillator, and only 1.14 times as fast as the linear interpolation oscillator.
   447|   447|   447|[...]
   448|   448|   448|These times do not include the times to build the tables, which are much larger for the non-interpolated oscillator. If table construction time is taken into account, interpolating oscillators may in fact have an advantage. If tables are constructed by summing sinusoids, then it costs much more to generate a table sample than to access the table. For example, if it costs 20 times as much to generate a table sample as to access it with the non-interpolating oscillator, if table sizes are 8192 for non-interpolating tables and 128 for quadratic interpolating tables, and if quadratic interpolation is 1.6 times the cost of non-interpolating access, then a table would have to be used for (8192 - 128) ´ 20 / 1.6 = 100,800 samples, or 2.3s of audio at a 44100 hz sampling rate. Otherwise, quadratic interpolation is faster (and linear interpolation would be faster still).
   449|   449|   449|[...]
   450|   450|   450|We have demonstrated that the table size required to achieve a given SNR is highly dependent upon the spectrum and the interpolation method. Software-based synthesizers should choose a table size and interpolation method that optimizes performance. Since musical spectra often exhibit significant rolloff, the performance of simple interpolation techniques can be quite good for table-lookup and other sample-rate conversion applications.
   451|   451|   451|- **[What’s your sine? Finding the right algorithm for digital frequency synthesis on a DSP](https://www.embedded.com/whats-your-sine-finding-the-right-algorithm-for-digital-frequency-synthesis-on-a-dsp/)**: DSP Implementing a
   452|   452|   452|[...]
   453|   453|   453|-speed sine-wave synthesizer in a programmable DSP is hardly a trivial task; it often requires a careful trade-off between memory and implementation complexity. The problem arises because the standard algorithms used to calculate Sin(x ), as well as any other transcendental function of x ,
   454|   454|   454|[...]
   455|   455|   455|the Taylor series expansion of
   456|   456|   456|[...]
   457|   457|   457|a time-consuming procedure. Hence
   458|   458|   458|[...]
   459|   459|   459|only a limited
   460|   460|   460|[...]
   461|   461|   461|real time.
   462|   462|   462|[...]
   463|   463|   463|alternative algorithms have been developed to boost the performance of the sine-wave generation. Table 1 presents some of the most popular
   464|   464|   464|[...]
   465|   465|   465|generation and computation algorithms used in a DSP.
   466|   466|   466|[...]
   467|   467|   467|Lookup table: The lookup table approach symbolizes the opposite to the Taylor Expansion, in the sense that no calculations are performed in real time. The values of the sine function are precalculated at evenly spaced phase points in the intervals [0,2π] and stored in memory during the initialization phase of the algorithm. In this way, a one-to-one mapping is established between each phase value and the address in memory where the table values are stored. The computation of the sine wave in real time entails approximating the input phase to one of the table indexes and retrieving the value stored at that index/address. The phase to index approximation constitutes one of the main sources of error of the algorithm. This error is in the order of the inverse of the table size, which is almost universally chosen to be a power of two.
   468|   468|   468|[...]
   469|   469|   469|Thisalgorithm is the fastest sine computation algorithm executing in only one instruction–the memory-read from the index corresponding to the input phase. Its drawback is that the table size becomes impractically large even for modest error goals; for instance an error of the order of 2–12 requires a table of 4,096 elements totaling 16 kbytes for 32-bit elements. Obviously this size can be reduced by a factor of four by exploiting the symmetry relations for Sin and Cos stated in Equation 2 but at the expense of the additional complexity of the algorithm needed to check the phase subinterval prior to lookup table addressing. The amount of overhead associated with this additional complexity more than doubles the execution time of the algorithm, making it a costly proposition. Another way to decrease the table size is to interpolate between the end points of the subinterval where the phase resides; this technique, however, again suffers the aforementioned performance drawbacks.
   470|   470|   470|[...]
   471|   471|   471|At first glance, the original idea suggested by Sunderland's technique seems to lend itself well for implementation in a programmable DSP. It succinctly proposes a reasonable trade-off: a sizeable decrease in the lookup table size at the expense of a modest increase in the number of calculations. The following reasons clarify why Sunderland's original works well with the specific TI C6x DSP family architecture.
   472|   472|   472|[...]
   473|   473|   473|2. The lookup table has a small footprint: in order to achieve maximum throughput, the lookup table must be stored in (precious) internal DSP memory, hence small size is highly desired.
   474|   474|   474|[...]
   475|   475|   475|format in both input and outputs
   476|   476|   476|[...]
   477|   477|   477|The C code for the algorithm is shown in Listing
   478|   478|   478|[...]
   479|   479|   479|1 with the corresponding generated assembly code. Note that
   480|   480|   480|[...]
   481|   481|   481|loop executes in three instructions compared with one for the lookup table. Furthermore, if the loop is unrolled by a factor of two,
   482|   482|   482|[...]
   483|   483|   483|instructions generating two pairs of sine/cosine outputs per iteration. This level of performance compares very well with the lookup table implementation;
   484|   484|   484|[...]
   485|   485|   485|still 2.5 times slower
   486|   486|   486|[...]
   487|   487|   487|uses 1/32 of the memory. Note as well
   488|   488|   488|[...]
   489|   489|   489|while the execution speed remains constant with the table sizes
   490|   490|   490|[...]
   491|   491|   491|the saving in memory use continues to growth at a rate of
   492|   492|   492|[...]
   493|   493|   493|sine/cosine generator.
   494|   494|   494|[...]
   495|   495|   495|to input an array with phase
   496|   496|   496|[...]
   497|   497|   497|constant phase increment Fr and
   498|   498|   498|[...]
   499|   499|   499|suffice even for the most demanding frequency-precision requirements. In
   500|   500|   500|[...]
   501|

# FM Synthesis Research: Library Audit & DSP Portability

### Library Portability & Dependencies
- **[ESP-DSP Library - ESP32 -  — Espressif DSP Library latest documentation](https://docs.espressif.com/projects/esp-dsp/en/latest/)**: The ESP-DSP it’s a C/C++ library that contains functions and classes for high performance calculations related applications. It includes implementations for different functionality, like: matrix multiplication, FFTs, filters, vector math operations and support functions for DSP applications development.
[...]
includes examples of
[...]
The ESP-DSP it’s the official DSP library for the Espressif chips. It contains optimized versions for ESP32 and ESP32-S3 chips, and will contain optimized versions for the new chips.
- **[Espressif DSP Library API Reference — Espressif DSP Library v1.4.0-5-g27a4881 documentation](https://espressif-docs.readthedocs-hosted.com/projects/esp-dsp/en/latest/esp-dsp-apis.html)**: # Espressif DSP Library API Reference ¶
[...]
To use the library, include `esp_dsp.h` header file into the source code.
[...]
## Signal (1D) Processing APIs ¶
[...]
Signal processing APIs use `dsps` prefix. The following modules are available:
[...]
- Dot-product - Calculates dot-product of two vectors
- FFT - Fast Fourier Transform functionality
- DCT - Discrete Cosine Transform functionality
- IIR - IIR filter functionality
- FIR - FIR filter functionality
- Math - Basic vector operations
- Conv - Convolution/correlation functionality
- Support - Support functions
- Window functions - FFT window generation functions
[...]
esp_err_t
[...]
dsps_dotprod_s16
[...]
dot product of two 16 bit vectors Dot product calculation for two signed 16 bit arrays: *dest += (src1[i] * src2[i]) >> (15-shift); i= [0..N) The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
[i] *
[...]
[i]); i= [0..N) The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
dot product of two float vectors with step Dot product calculation for two floating point arrays: *dest += (src1[i*step1] * src2[i*step2]); i= [0..N) The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
Complex FFT of radix 2 The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
C and could be compiled and run on
[...]
C and could be compiled and run on any platform
[...]
DCT type II of radix 2, unscaled Function is FFT based The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
C and could be compiled and run on any platform. The extension (_ae32
[...]
is optimized for ESP32 chip
[...]
Function implements FIR filter The extension (_ansi) uses ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
extension (_ansi) uses ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip
[...]
int32_t `` `dsps_fird_s16_ansi`(fir_s16_t * fir, const int16_t * input, int16_t * output, int32_t len) ¶
[...]
Function implements FIR filter with decimation The extension (_ansi) uses ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
esp_err_t `` `dsps_fir_init_f32`(fir_f32_t * fir, float * coeffs, float * delay, int coeffs_len) ¶
[...]
Function initialize structure for 3
[...]
_err_
[...]
32`(
[...]
2_t *
[...]
compiled and run on any platform
[...]
esp_err_t `` `dsps_fird_init_s16`(fir_s16_t * fir, int16_t * coeffs, int16_t * delay, int16_t coeffs_len, int16_t decim, int16_t start_pos, int16_t shift) ¶
[...]
initialize structure for 16 bit Decimation FIR filter Function initialize structure for 16 bit signed fixed point FIR filter with decimation The implementation use ANSI C and could be compiled and run on any platform
[...]
and could be compiled and run on any platform
[...]
The extension (_ae32
[...]
is optimized for ESP32 chip
[...]
esp_err_t
[...]
_add_
[...]
2_ansi`(const
[...]
_ansi`(const
[...]
* output,
[...]
Matrix Operations APIs ¶
[...]
Matrix operations APIs use `dspm` prefix. The following modules are available:
[...]
The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
[...]
esp_err_t `` `dspm_mult_s16_ansi`(const int16_t * A, const int16_t * B, int16_t * C, int m, int n, int k, int shift) ¶
[...]
Matrix multiplication for two signed 16 bit fixed point matrices: C[m][k] = (A[m][n] * B[n][k]) >> (15- shift) The extension (_ansi) use ANSI C and could be compiled and run on any platform. The extension (_ae32) is optimized for ESP32 chip.
- **[Espressif DSP Library Examples - ESP32 -  — Espressif DSP Library latest documentation](https://docs.espressif.com/projects/esp-dsp/en/latest/esp-dsp-examples.html)**: Espressif DSP Library Examples - ESP32 - — Espressif DSP Library latest documentation
[...]
# Espressif DSP Library Examples¶
[...]
## List of esp-dsp Examples¶
[...]
Signal processing APIs use`dsps` prefix. The following modules are available:
[...]
This example demonstrates how to use basic math functions from esp-dsp library. The example does the following steps:
[...]
### Dot-product
[...]
The example demonstrates how to use dotprod dsps_dotprod_f32 from esp-dsp library. Example does the following steps:
[...]
This example demonstrates how to use FFT functionality from esp-dsp library. Example does the following steps:
[...]
### FFT Window¶
[...]
This example demonstrates how to use Window and FFT functionality from esp-dsp library. Example does the following steps:
[...]
This example demonstrates how to use FFT functionality from esp-dsp library. Example does the following steps:
[...]
following steps:
[...]
This example demonstrates
[...]
. Example does the following steps:
[...]
This example emulate system with IMU sensors and show how to use Extended Kalman Filter (EKF), with 13 values states vector, to estimate gyroscope errors and calculate system attitude. Also, this example show how to use esp-dsp library to operate with matrices and vectors.
- **[modules/fft/fixed/dsps_fft2r_sc16_ansi.c at master · espressif/esp-dsp](https://github.com/espressif/esp-dsp/blob/master/modules/fft/fixed/dsps_fft2r_sc16_ansi.c)**: > CONFIG_DSP
[...]
_w_table_sc16, dsps_fft_w_table_sc16_size >> 1);
    if (result != ESP_OK) {
[...]
;
    }
    ds
[...]
;
[...]
esp_err_t dsps_fft2r_sc16_ansi_(int16_t *data, int N, int16_t *sc_table)
{
    if (!dsp_is_power_of_two(N)) {
        return ESP_ERR_DSP_INVALID_LENGTH;
    }
    if (!dsps_fft2r_sc16_initialized) {
        return ESP_ERR_DSP_UNINITIALIZED;
    }

    esp_err_t result = ESP_OK;

    uint32_t *w = (uint32_t *)sc_table;
    uint32_t *in_data = (uint32_t *)data;

    int ie, ia, m;
    sc16_t cs;// c - re, s - im
    sc16_t m_data;
    sc16_t a_data;

    ie = 1;
    for (int N2 = N / 2; N2 > 0; N2 >>= 1) {
        ia = 0;
        for (int j = 0; j < ie; j++) {
            cs.data = w[j];
            //c = w[2 * j];
            //s = w[2 * j + 1];
            for (int i = 0; i < N2; i++) {
                m = ia + N2;
                m_data.data = in_data[m];
                a_data.data = in_data[ia];
                //data[2 * m] = data[2 * ia] - re_temp;
                //data[2 * m + 1] = data[2 * ia + 1] - im_temp;
                sc16_t m1;
                m1.re = xtfixed_bf_1(a_data.re, cs.re, m_data.re, cs.im, m_data.im, 16);//(a_data.re - temp.re + shift_const) >> 1;
                m1.im = xtfixed_bf_2(a_data.im, cs.re, m_data.im, cs.im, m_data.re, 16);//(a_data.im - temp.im + shift_const) >> 1;
                in_data[m] = m1.data;

                //data[2 * ia] = data[2 * ia] + re_temp;
                //data[2 * ia + 1] = data[2 * ia + 1] + im_temp;
                sc16_t m2;
                m2.re = xtfixed_bf_3(a_data.re, cs.re, m_data.re, cs.im, m_data.im, 16);//(a_data.re + temp.re + shift_const) >> 1;
                m2.im = xtfixed_bf_4(a_data.im, cs.re, m_data.im, cs.im, m_data.re, 16);//(a_data.im + temp.im + shift_const)>>1;
                in_data[ia] = m2.data;
                ia++;
            }
            ia += N2;
        }
        ie <<= 1;
    }
    return result;
}
[...]
esp_err_t dsps_bit
[...]
rev_sc16_ansi(int16_t *data, int N)
{
    if (!dsp_is_power_of_two(N)) {
        return ESP_ERR_DSP_INVALID_LENGTH;
    }
    esp_err_t result = ESP_OK;
[...]
j, k;
[...]
uint32_t temp;
    uint32_t *in_data = (uint3
[...]
1; i <
[...]
(k <=
[...]
(i < j) {
[...]
temp = in_data[j];
[...]
_data[j] = in
[...]
data[i
[...]
data[i] = temp
[...]
esp_err_
[...]
cplx2reC_sc16(int16_t *data, int N)
[...]
esp_err_t result
[...]
esp_err_t dsps_cplx2real_sc16_ansi(int16_t *data, int N)
{

    int order = dsp_power_of_two(N);
    sc16_t *table = (sc16_t *)dsps_fft_w_table_sc16;
    sc16_t *result = (sc16_t *)data;
    // Original formula...
    // result[0].re = result[0].re + result[0].im;
    // result[N].re = result[0].re - result[0].im;
    // result[0].im = 0;
    // result[N].im = 0;
    // Optimized one:
    int16_t tmp_re = result[0].re;
    result[0].re = (tmp_re + result[0].im) >> 1;
    result[0].im = (tmp_re - result[0].im) >> 1;

    sc16_t f1k, f2k;
    for (int k = 1; k <= N / 2 ; ++k ) {
        sc16_t fpk = result[k];
        sc16_t fpnk;
        fpnk.re = result[N - k].re;
        fpnk.im = result[N - k].im;
        f1k.re = fpk.re + fpnk.re;
        f1k.im = fpk.im - fpnk.im;
        f2k.re = fpk.re - fpnk.re;
        f2k.im = fpk.im + fpnk.im;

        int table_index = reverse(k, N, order);

        // float c = -dsps_fft_w_table_fc32[table_index*2+1];
        // float s = -dsps_fft_w_table_fc32[table_index*2+0];
        sc16_t w = table[table_index];

        sc16_t tw;
        {
            int re = (w.re * f2k.im - w.im * f2k.re) >> 15;
            int im = (+w.re * f2k.re + w.im * f2k.im) >> 15;
            tw.re = re;
            tw.im = im;
        }

        result[k].re = (f1k.re + tw.re) >> 2;
        result[k].im = (f1k.im - tw.im) >> 2;
        result[N - k].re = (f1k.re - tw.re) >> 2;
        result[N - k].im = -(f1k.im + tw.im) >> 2;
    }
    return ESP_OK;
}
- **[guilhAbreu/EspMath](https://github.com/guilhAbreu/EspMath)**: Library that implement tools to perform math operations optimized for ESP32 devices
[...]
This library is intended to provide valuable tools to perform math operations such as arrays multiplication, convolution, etc, on ESP32 devices. ESP32-S3 chips provide **DSP** (Digital Signal Processing) instructions to accelerate the procedure.
[...]
At DSP, you can find all the dsp accelerated operations. These functions make use of the Processor Instruction Extensions (PIE) to accelerate operations and work only with 16 bytes aligned memory. The class Array already uses them to perform operations.
[...]
DSP acceleration targets ESP32-S3 devices and is not available for other chips. To use them, just include esp_dsp.
[...]
## Fixed Point Computation
[...]
In this project, you will find many tools to accelerate the computation of fixed-point (16 bits) data such as fixed, which was designed to ease fixed-point manipulation. For fixed-point arrays, Array and DSP provide multiple features to ease and accelerate the computation as well.

### Lightweight DSP Alternatives
- **[vitalsong/dsplib](https://github.com/vitalsong/dsplib)**: Modern C++ DSP library with MATLAB-like syntax
[...]
A C++ DSP library with MATLAB-like syntax for readable and safe signal processing.
[...]
`dsplib` is, essentially, my personal collection of DSP screwdrivers.

It has been slowly assembled over several years and used across many projects.
The reason is simple: a lot of C++ DSP code is painful to read, easy to misuse,
and surprisingly good at reproducing the same bugs in slightly different forms.

Raw pointers in public APIs, implicit assumptions about buffer sizes, and
hand-written
[...]
tend to age poorly.
[...]
`dsplib` is an attempt to build a **1D DSP library with a human face**:
modern C++ abstractions, explicit intent, and a syntax that does not fight the
person writing the code.

The library favors clarity and correctness first — and then works very hard to
make it fast.
[...]
The table below highlights key conceptual differences between dsplib and
MATLAB / NumPy, beyond simple syntax similarities.
[...]
| dsplib | matlab | numpy |
| --- | --- | --- |
| x * x | x .* x | x * x |
| zeros(20) | zeros(20, 1) | zeros(20) |
| x.slice(0,10) = 1 | x(1:10) = 1 | x[0:10] = 1 |
| x.slice(2,end) = 1 | x(3:end) = 1 | x[2:] = 1 |
| x.slice(2, 4) = {1, 2} | x(3:4) = [1, 2] | x[2:4] = [1, 2] |
| x.slice(0, -1) | x(1:end-1) | x[0:-1] |
[...]
> Only 1-D arrays with element-wise operations are currently supported.
[...]
The behavior of slices is as close as possible to numpy. Except for cases with invalid indexes, in which case numpy does not throw an exception.
[...]
### Fast Fourier Transform:
[...]
The `FFT` implementation has no radix size limitations. 
It supports power-of-two, prime, and semiprime radices.

The tables for the `FFT` are stored in the `LRU` cache and can be recalculated (if the pipeline uses many different bases). Use the `FftPlan` object to avoid this.
[...]
If your platform has a faster implementation, you can set the `DSPLIB_EXCLUDE_FFT=ON` option and implement the `get_fft_plan` functions (see the `lib/fft/fftw.cpp` example). 
You can also select the type of FFT backend via the `DSPLIB_FFT_BACKEND` option (dsplib, fftw, ne10[float]).
[...]
### ⚠️ Thread Safety & Memory Notice
[...]
The standard implementation is **thread-safe** because all caches (primarily FFT-related) use `thread_local` storage. 
**Memory warning:** This may increase memory consumption if used carelessly – please avoid spreading processing across hundreds of threads.
[...]
FFTW3 backend is wrapped with a **static
[...]
** (excluding
[...]
fftw_execute`
[...]
) and is also thread-safe.
[...]
- CMake (>=3.15)
- C++17 compiler (exceptions can be disabled)

`dsplib` is designed with portability in mind and avoids platform-specific
dependencies wherever possible.
[...]
If your platform has a reasonably complete C++17 compiler and CMake support,
there is a good chance dsplib will build there as well.
[...]
Known to work in production on:
[...]
- Android (API 27, 29, ARMv7 / ARMv8)
- Linux (GCC, Clang, MinGW)
- Windows (MSVC, MinGW)
- macOS (Clang)
- WebAssembly (Emscripten)
- Custom Buildroot-based ARM toolchains
[...]
The implementation of non-power-of-two FFT is based on the general factorization algorithm. It is usually slower, but not critical.

For prime and semi-prime numbers, the czt algorithm is used, which can be significantly slower (but not as slow as regular DFT).
[...]
## TODO (this is a wishlist, not a
[...]
- Add matrix syntax support;
- Add custom allocator for `base_array ` type;
- Add audioread/audiowrite functions (optional libsndfile?);
- Add chain syntax like `fft(x)->abs2()->pow2db()`;
- `SOS` filters;
- Multichannel resampler;
- Thread-safe storage for `FFT` (not `thread_local`);
- Add `chirp`, `conv`, `filter`, `dzt`, `remez` etc.
- Real/Imag slice for `arr_cmplx`;
- **[aneesharnavch/microdsp](https://github.com/aneesharnavch/microdsp)**: A Digital Signal Processing Library to process and parse data on any embedded platform
[...]
- Stars: 0
[...]
: 0
[...]
ers: 0
[...]
.4%),
[...]
(3.6%)
[...]
- License: MIT License (MIT)
[...]
Contributors: 1 (
[...]
microdsp (micro digital signal processing) is a lightweight signal processing library made for resource-constrained signal processing on microcontrollers. Refer to examples for detailed instructions on implementation, and refer to the documentation for further information.
[...]
All statistical, calculus, filter, and transform functions are available in streaming (sliding window) mode for live sensor/microphone data processing.
[...]
**Lengths:** All transform/filter lengths must be powers of two
[...]
/filter sizes
[...]
be smaller than
[...]
**Memory:** Library uses stack-based arrays by default; memory use is proportional to array sizes and filter orders.
- **[lighttransport/nanosnap](https://github.com/lighttransport/nanosnap)**: Nanoscale Signal, Noise and Audio Processing library in C++11
[...]
# NanoSNAP, Nanoscale Signal, Noise and Audio Processing library in C++11
[...]
NanoSNAP is a small and portable signal, audio and noise processing library in C++11. 🤞
NanoSNAP depends only on C++11 STL.
[...]
- For running TTS(Text-to-speech) and ASR(Automatic Speech Recognition) on C++ Embedded device.
- Image processing with neural netowork inference on C++ and Embedded device.
- Implement audio and speech feature(e.g. using `rfft`, `mfcc` `stft`, `istft`) on your C++ machine learning library.
[...]
- CMake(for building examples and tests, and build NanoSNAP as submodules)
- C++11 compiler
[...]
| NanoSNAP | Description | Python equivalent |
| --- | --- | --- |
| `reshape_with_strides` | Create an array with the given shape and strides. | `numpy.lib.stride_tricks.as_strided` |
| `convolve` | 1D convolution | `numpy.convolve` |
| `loadtxt` | Load 1D or 2D array | `numpy.loadtxt` |
| `savetxt` | Save 1D or 2D array | `numpy.savetxt` |
[...]
| NanoSNAP | Description | Python equivalent |
| --- | --- | --- |
| `random_uniform` | Uniform random number | `numpy.random.rand` |
| `random_shuffle` | Randomly shuffle array | `numpy.random.shuffle` |
[...]
| NanoSNAP | Description | Python equivalent |
| --- | --- | --- |
| `rfft` | Real 1D FFT | `numpy.fft.rfft` |
| `ifft` | Inverse Complex FFT | `numpy.fft.ifft` |
[...]
### Scipy
[...]
| NanoSNAP | Description | Python equivalent |
| --- | --- | --- |
| `lfilter` | Filter data along one-dimension with an IIR or FIR filter. | `scipy.signal.lfilt` |
| `medfilt` | Median filter | `scipy.signal.medfilt` |
| `wav_read` | Read .WAV file | `scipy.io.wavfile.read` |
| `wav_write` | Save .WAV file | `scipy.io.wavfile.write` |
[...]
| NanoSNAP | Description | Python equivalent |
| --- | --- | --- |
| `stft` | Short Term Fourier Transform | `librosa.stft` |
| `istft` | Inverse STFT | `librosa.istft` |
| `mel` | Create a Filterbank matrix to combine FFT bins into Mel-frequency bins | `librosa.filters.mel` |
- **[KFR | Fast, modern C++ DSP framework](http://kfrlib.com/)**: KFR | Fast, modern C++ DSP framework
[...]
# FAST, MODERN C++ DSP FRAMEWORK
[...]
KFR is built for developers who need a powerful toolkit for audio and dsp applications
[...]
KFR 6 has been released with DFT performance improvements, multidimensional DFT, easier setup and many new features.
[...]
## Use cases
[...]
- Audio and music applications
- AudioUnits/VST plugins
- Runtime data analysis
- AI data augmentation
- Scientific applications
- Data visualisation
[...]
- Discrete Fourier Transform (aka DFT) and Fast Fourier Transform (aka FFT)
- Biquad filters and filter design
- IIR filter design
- FIR filters and design
- High quality Sample Rate Conversion
- EBU R 128 Loudness
- Window functions
- Goertzel, Stereo mixing, DC removal, A-, B- and C- weighting
- Mathematical and statistical functions
- Audio file reading/writing
- Pseudorandom number generator
[...]
## Why KFR
[...]
#### It's Fast
[...]
KFR functions and algorithms are carefully designed and implemented for maximum performance
[...]
#### DSP, FFT and more
[...]
KFR has one of the fastest implementation of FFT as well as filter processing, filter design, convolution and resampling
[...]
It supports Intel, AMD, ARM and Apple cpus as well as all available SIMD for maximum speed on any device
[...]
- It is open source and develops with the help of community
- Writing your own algorithms based on KFR framework is easy

- Its functions are constantly tested for correctness and accuracy
- Trusted by companies, organisations and individual developers since 2016
[...]
## Code examples and performance
[...]
## Performing DFT/FFT
[...]
```
// perform real DFT/FFT
univector<float> input;
univector<complex<float>> output = realdft(input);
[...]
// Apply filter using fast implementation
output    =
[...]
iquad<
- **[mohabouje/eDSP](https://github.com/mohabouje/edsp)**: A cross-platform DSP library written in C++ 11/14. This library harnesses the power of C++ templates to implement a complete set of DSP algorithms.
[...]
*eDSP* (easy Digital Signal Processing) is a digital signal processing framework written in modern C++ that implements some of the common functions and algorithms frequently used in digital signal processing, audio engineering & telecommunications systems.
[...]
It aims to provide efficient and streamlined base calculations while at the same time having a straightforward and easy-to-use interface similar to another numeric language like MATLAB. The library integrates a C++ and Python interfaces and supports Windows, Linux, Mac OS, iOS and Android.
[...]
*eDSP is a modern framework written for modern C++ developers*. It is an alternative for those who want a powerful template DSP library that keeps a simple STL-like API. *eDSP* is a framework that:
[...]
- Is implemented as a **Headers only** library, just copy and use.
- Is **Template library**: supports single and double floating point, and fixed-point operations.
- Integrates an **easy** to use interface, **STL-like API**.
- Provides **fast** implementations: check the available benchmarks.
- Aims to be **cross-platform**
- Supports modern **C++11/14/17 features**
- Implements a simple interface compatible with C
- Integrates bind for different languages: **Python, Go and JavaScript**
[...]
*eDSP* relies in a modern C++ compiler and some standard third-party dependencies. The idea is to build a framework compatible with most of the modern platforms with the minimum modifications.

Those are the supported platforms:
[...]
- Linux, FreeBSD
[...]
- Windows (msvc
[...]
cygwin)
[...]
eDSP is organized in different modules. Each module integrates different related features. This is the list of the available modules:
[...]
- *Audio Coder*: encoder/decoder for different audio formats.
- *Digital filters*: Biquad, FIR, IIR, moving average, moving median etc.
- *Spectral analysis*: Fourier transform, Hilbert transform, Hartley transform, cepstrum, periodogram etc.
- *Oscillators*: sinusoidal, square, triangular and sawtooth signals.
- *Random*: binary sequence, uniform distribution, Bernoulli, Weibull, Student, Fisher etc.
- *Math*: extra functionalities not available in the C++ standard.
- *Noising*: white noise, brown noise, pink noise, Perlin noise etc.
- *Algorithms*: clipper, amplifier, lineal-space, logarithmic-space , padder, normalizer etc.
- *Quantizer*: u-law, A-law etc.
- *Statistics*: moment, median, skewness, kurtosis etc.
- *Windowing*: Bartlett, Hamming, Kaiser, Welch, Blackman etc.
- *Modulation*: AM, FM, M-PSK, OFDM etc.
- *MIR*: feature engineering for MIR (Music Information Retrieval) applications.
- *Equalization*: LMS, RLS etc.

