     1|# Stochastic Music Generation Research
     2|
     3|
     4|## 3. Medium-Complexity Algorithms
     5|### Gaussian Processes
     6|- **[tinygp v0.3.1](https://pypi.org/project/tinygp/)**: The tiniest of Gaussian Process libraries
     7|[...]
     8|## Readme
     9|
    10| 
    11| 
    12| tinygp 
    13| the tiniest of Gaussian Process libraries 
    14| 
    15| 
    16| 
    17| 
    18| 
    19| 
    20| 
    21| 
    22| 
    23| 
    24| 
    25| 
    26|
    27|`tinygp` is an extremely lightweight library for building Gaussian Process (GP)
    28|models in Python, built on top of `jax`. It has
    29|a [nice interface][api-ref], and it's [pretty fast][benchmarks]. Thanks to
    30|`jax`, `tinygp` supports things like GPU acceleration and automatic
    31|differentiation.
    32|[...]
    33|| Package | Constraint |
    34|| --- | --- |
    35|| equinox | |
    36|| jax | |
    37|- **[tinygp — tinygp](https://tinygp.readthedocs.io/)**: The tiniest of Gaussian Process libraries.
    38|
    39|`tinygp` is an extremely lightweight library for building Gaussian Process (GP) models in Python, built on top of jax. It has a nice interface, and it’s pretty fast (see Benchmarks). Thanks to `jax`, `tinygp` supports things like GPU acceleration and automatic differentiation.
    40|
    41|How to find your way around?
    42|[...]
    43|🖥 A good place to
    44|[...]
    45|is with the
    46|[...]
    47|📖 For all
    48|[...]
    49|out the User Guide, including the full API documentation.
    50|- **[musicgen-minimal v1.1.2](https://pypi.org/project/musicgen-minimal/)**: - Email: MusicGen Minimal Team 
    51|- License: MIT
    52|- Python: >=3.8
    53|- Status: 5 - Production/Stable
    54|- Keywords: music, generation, ai, text-to-music, musicgen, audio, transformers
    55|- PyPI: https://pypi.org/project/musicgen-minimal/
    56|[...]
    57|Gen-Minimal v1.
    58|[...]
    59|Text-to-music generation that actually works. No bullshit, just music.
    60|[...]
    61|- 🎵 Generates music from text descriptions
    62|- 🚀 Extended generation beyond 30 seconds (NEW)
    63|- 📦 Batch processing from CSV files (NEW)
    64|- 🎧 MP3 output support (NEW)
    65|- 📊 Real-time progress bars (NEW)
    66|- ✅ Uses Facebook's MusicGen model
    67|- 💻 Works on CPU (slow) or GPU (fast)
    68|[...]
    69|- No web interface (yet)
    70|- No real-time generation
    71|- No style transfer
    72|- No MIDI export
    73|-
    74|[...]
    75|⚠️ **Reality Check**: This package downloads 2.7GB of dependencies (PyTorch, etc.) on first install.
    76|[...]
    77|pip install musicgen-minimal
    78|[...]
    79|**First install includes:**
    80|[...]
    81|- PyTorch (2.1GB) - ML framework
    82|- Transformers (400MB) - HuggingFace models
    83|- Other dependencies (300MB)
    84|- **Total**: ~2.7GB download, ~7GB disk space
    85|[...]
    86|- Small model: 300MB (recommended)
    87|- Medium model: 1.5GB (better quality)
    88|- Large model: 3.2GB (best quality)
    89|[...]
    90|```bash
    91|# Generate 10 seconds of music
    92|musicgen generate "upbeat jazz piano" --duration 10
    93|[...]
    94|### Batch Processing
    95|[...]
    96|# Parallel processing with 4 workers
    97|musicgen batch jobs.csv --workers 4
    98|[...]
    99|### Python API
   100|[...]
   101|```python
   102|from musicgen import quick_generate
   103|[...]
   104|# Generate 60-second MP3
   105|quick_generate(
   106|    "cinematic trailer music",
   107|    "trailer.mp3", 
   108|    duration=60,
   109|    format="mp3",
   110|    bitrate="320k"
   111|)
   112|```
   113|[...]
   114|## Performance (Brutal Honesty)
   115|[...]
   116|**CPU Performance (Most Users)**
   117|[...]
   118|- **Model loading**: ~30 seconds
   119|- **Generation speed**: 0.1x realtime (10x slower)
   120|- **30-second song**: ~5 minutes
   121|- **2-minute song**: ~20 minutes
   122|- **Memory usage**: ~4GB RAM
   123|[...]
   124|**GPU Performance (CUDA)**
   125|[...]
   126|- **Model loading**: ~5 seconds
   127|- **Generation speed**: 2-5x realtime
   128|- **30-second song**: ~10 seconds
   129|- **2-minute song**: ~40 seconds
   130|- **Memory usage**: ~4GB VRAM
   131|[...]
   132|Because it actually works. The original project had 343 files and didn't run.
   133|[...]
   134|## What's New in v1.1.0
   135|[...]
   136|### Extended Generation
   137|[...]
   138|- Generate music longer than 30 seconds (up to unlimited duration)
   139|- Automatic segment blending with crossfade
   140|- Progress tracking for multi-segment generation
   141|[...]
   142|### Batch Processing
   143|[...]
   144|- Process multiple tracks from CSV files
   145|- Parallel generation with multiprocessing
   146|- Mixed format support (WAV and MP3 in same batch)
   147|[...]
   148|- 75-90% smaller files than WAV
   149|- Configurable bitrate (128k-320k)
   150|- Graceful fallback if dependencies missing
   151|[...]
   152|### Progress Bars
   153|[...]
   154|- Real-time progress for all operations
   155|- ETA calculation for long generations
   156|- Optional --no-progress for automation
   157|[...]
   158|| Package | Constraint |
   159|| --- | --- |
   160|| torch | >=2.0.0 |
   161|| transformers | >=4.35.0 |
   162|| soundfile | >=0.12.0 |
   163|| numpy | >=1.21.0 |
   164|| typer | >=0.9.0 |
   165|| rich | >=13.0.0 |
   166|| requests | >=2.25.0 |
   167|| pandas | >=1.5.0 |
   168|[...]
   169|| Package | Constraint |
   170|| --- | --- |
   171|| pydub | >=0.25.0 |
   172|| fastapi | >=0.100.0 |
   173|| uvicorn | [standard]>=0.23.0 |
   174|| pydantic | >=2.0.0 |
   175|| pytest | >=7.0.0 |
   176|| pytest-cov | >=4.0.0 |
   177|| black | >=23.0.0 |
   178|| isort | >=5.12.0 |
   179|| flake8 | >=6.0.0 |
   180|| mypy | >=1.0.0 |
   181|| musicgen-minimal | [api,audio,dev] |
   182|- **[dfm/tinygp: The tiniest of Gaussian Process libraries - GitHub](https://github.com/dfm/tinygp)**: # Repository: dfm/tinygp
   183|
   184|The tiniest of Gaussian Process libraries
   185|
   186|- Stars: 340
   187|- Forks: 33
   188|- Watchers: 340
   189|- Open issues: 30
   190|- Primary language: Python
   191|- Languages: Python
   192|- License: MIT License (MIT)
   193|- Default branch: main
   194|- Homepage: https://tinygp.readthedocs.io
   195|- Created: 2021-02-14T15:51:26Z
   196|- Last push: 2026-04-06T20:54:31Z
   197|- Contributors: 20 (top: dfm, pre-commit-ci[bot], dependabot[bot], yadav-sachin, ywx649999311, theorashid, andrewfowlie, Copilot, dhuppenkothen, hannes-holey)
   198|- Releases: 18
   199|- Latest release: v0.3.1 (2026-03-15T14:44:12Z)
   200|
   201|---
   202|
   203| 
   204| 
   205| tinygp 
   206| the tiniest of Gaussian Process libraries 
   207| 
   208| 
   209| 
   210| 
   211| 
   212| 
   213| 
   214| 
   215| 
   216| 
   217| 
   218| 
   219|
   220|`tinygp` is an extremely lightweight library for building Gaussian Process (GP)
   221|models in Python, built on top of `jax`. It has
   222|a [nice interface][api-ref], and it's [pretty fast][benchmarks]. Thanks to
   223|`jax`, `tinygp` supports things like GPU acceleration and automatic
   224|differentiation.
   225|
   226|Check out the docs for more info: [tinygp.readthedocs.io][docs]
   227|
   228|[api-ref]: https://tinygp.readthedocs.io/en/latest/api/index.html
   229|[benchmarks]: https://tinygp.readthedocs.io/en/latest/benchmarks.html
   230|[docs]: https://tinygp.readthedocs.io
   231|- **[rdpoor/pygmu](https://github.com/rdpoor/pygmu)**: Python Generative Music framework
   232|[...]
   233|pygmu is a Python Generative Music framework. It comprises a collection of _processing elements_ that you connect together to create works of music. Unlike most other frameworks, pygmu does not make a strong distinction between voices and notes: everything is computed at the sampling rate.
   234|[...]
   235|There are three fundamental objects upon which all of pygmu is built:
   236|[...]
   237|- **Extent:** An Extent encapsulates a starting time (measured in samples) and an ending time (also measured in samples).
   238|- **Processing Element:**: Each processing element has a constructor, where you specificy parameters, and a `render(extent)` function which, when invoked, asks the processing element to produce sample data between `extent.start()` and `extent.end()`.
   239|- **frames:** Sample data is passed around as a two-dimensional array, where each row is a channel (e.g. stereo frames will have two rows) and each columm is an individual sample (mono, stereo or multi-channel).
   240|[...]
   241|def generate_note(start_time, duration, hz, amp):
   242|    """
   243|    Return a Processing Element such that when you call render(), it produces
   244|    a tone at the desired time with a given duration, frequency and amplitude.
   245|    """
   246|    # Use an Extent to define the start and end times of the note
   247|    extent = pg.Extent(s_to_f(start_time), s_to_f(start_time + duration))
   248|
   249|    # We use a BlitSaw (bandwidth limited sawtooth wave generator) to generate 
   250|    # the tone, passing its output through a gain stage to adjust the amplitude 
   251|    # and then through a "CropPE" to specify the start and end times.
   252|
   253|    # We could chain together the processing elements like this...
   254|    # saw_pe = pg.BlitSawPE(frequency=hz, frame_rate=SRATE)
   255|    # saw_pe = pg.GainPE(saw_pe, amp)          # Adjust the amplitude
   256|    # saw_pe = pg.CropPE(saw_pe, extent)       # Crop start and end times
   257|    # return saw_pe                            # Return the result
   258|
   259|    # ... but PygMu provides shorthand chaining methods for many common
   260|    # functions, so you can write the whole thing in one line if you prefer:
   261|    return pg.BlitSawPE(frequency=hz, frame_rate=SRATE).gain(amp).crop(extent)
   262|[...]
   263|# At this point, we have defined HOW the final piece will be generated, but it 
   264|# hasn't been generated yet.  The Transport `play()` method will repeatedly call
   265|# `tristan.render()` to request samples, sending the result to the DAC on your
   266|# computer.
   267|[...]
   268|(tristan).
   269|[...]
   270|The example above has eleven processing elements, connected as shown below. The 
   271|important thing to notice is that each processing element is a black box that 
   272|will produce frames whenever its `render()` method is called. A graphical 
   273|representation of the above looks like this:
   274|[...]
   275|The `Transport` object is not a processing element itself: it doesn't provide a 
   276|render() function. Instead, when its play() method is called, it starts calling 
   277|render() on the processing element connected to its input, which in this case is 
   278|the `mix` processing element. The `mix` PE in turn calls each of its inputs 
   279|(the `crop` PEs), which in turn call _their_ inputs and so on, to generate the 
   280|tones.
   281|[...]
   282|In computer science terms, a pygmu composition is a _directed acyclic graph_ in 
   283|which the root node (Transport) "pulls" frames from the rest of the network.
   284|[...]
   285|In pygmu, buffers of sample data are exchanged using `frames`, which are implemented using 
   286|two-dimensional numpy arrrays of 32 bit floating point values. Every call to `pe.render()` will return a frames object.
   287|[...]
   288|You can use `np.vstack()` to convert a mono buffer into stereo without changing gain. Note that you can use 1-D or 2-D arrays:
   289|[...]
   290|The `reshape()` function lets you reshape an array to a given number of rows
   291|and columns, as long as rows * columns equals the length of the array. The
   292|simplified form is `.reshape(n_rows, n_columns)`:
   293|[...]
   294|- Should compressors / limiters accept multi-
   295|[...]
   296|- If
   297|[...]
   298|an error). We could create a pass-
   299|[...]
   300|PE that disc
   301|
   302|### Rule-based Grammars
   303|- **[[PDF] Musical Composition with Stochastic Context-Free Grammars](https://inria.hal.science/hal-01257155/document)**: Musical Composition with Stochastic Context-Free
   304|Grammars
   305|[...]
   306|Abstract. In this paper we discuss the design of a generator of musical sequences for the purpose of aiding musicians in their compositional
   307|[...]
   308|. Specifically, in the case
   309|[...]
   310|of the main structure of the suggestion a stochastic context-free grammar
   311|[...]
   312|was developed to generate harmonic progressions, in the case of rhythms
   313|[...]
   314|a probabilistic model based on accents was also developed, and finally,
   315|[...]
   316|to state individual notes of the suggestion another probabilistic model
   317|[...]
   318|based in the dissonance of intervals was used. Each one of these models
   319|[...]
   320|were structured upon J.S. Bach’s rules of homophonic composition as
   321|[...]
   322|this was the target musical genre of the work.
   323|[...]
   324|Key words: Musical Composition, Stochastic Context-Free Grammars,
   325|[...]
   326|2 explains musical
   327|[...]
   328|concepts relevant to the work done here, section 3 describes the context free
   329|[...]
   330|grammars and its stochastic extension, section 4 shows the composition assistant
   331|[...]
   332|proposed and also the abstracted models for the composition process (progression
   333|[...]
   334|generator, rhythm generator and notes assigner), in section 5 we show and detail
   335|[...]
   336|finally section 6 draws some
   337|[...]
   338|3 Context-Free Grammars
   339|[...]
   340|A grammar is context-free if for each one of the production rules, its right
   341|[...]
   342|side is made up of only one non-terminal symbol, meaning if the production
   343|rules follow the next form:
   344|[...]
   345|A stochastic context-free grammar is an extension of context-free grammars
   346|[...]
   347|where each production rule has an associated probability representing the plausibility of the rule . Then, a stochastic context-free grammar can be seen as
   348|[...]
   349|production rule with the following form:
   350|[...]
   351|Ai → E [pi]
   352|[...]
   353|where A ∈ N, E is any string in (Σ ∪ N)
   354|[...]
   355|+, and pi
   356|is the probability of the
   357|rule Ai. The probabilities must fulfill the next statements:
   358|[...]
   359|The process of musical composition was abstracted as a sequence of steps to
   360|[...]
   361|4.1 Progression Generator
   362|[...]
   363|A very convenient way to model this is with a stochastic grammar with each
   364|rule meaning the possible flow the main structure of the song may take. Each
   365|symbol may represent a chord or an expandable progression that itself may
   366|transform again in a chord or another progression (depending on the length of
   367|[...]
   368|the song the user suggested).
   369|[...]
   370|We modeled a stochastic context free grammar that generates the harmonic
   371|progression the song has. Also the grammar reflects the harmonic basis of the
   372|genre we wished to model (e.g. ending the song in the tonic degree) and giving
   373|appropriate probabilities to each rule so the result may sound consistent and
   374|coherent. These probabilities were found from a deep study of J.S. Bach pieces.
   375|[...]
   376|The grammar is fed to the program as a plain text file, so the rules and
   377|[...]
   378|may be changed by the user to reflect other
   379|[...]
   380|or his own
   381|[...]
   382|The algorithm for generating strings from a grammar was created due to the
   383|important parsers like Earley [3], Eugene Charniak’s parser [4] or CKY (CockeKasami-Younger [5]) being only to determine if a string belongs to a language,
   384|[...]
   385|We implement the algorithms in Python using Gtk+ as a graphical interface.
   386|For the transcription to a known format we use Lilypond (http://lilypond.
   387|[...]
   388|] [12]
   389|[...]
   390|) and the models
   391|[...]
   392|for example random, Markovbased, stochastic generation
   393|[...]
   394|generation. The novelty
   395|[...]
   396|approach in contrast to the
   397|[...]
   398|the abstraction of compositional
   399|[...]
   400|the three modules
   401|[...]
   402|of homophonic composition.
   403|[...]
   404|We plan to pursue this work in two directions: (a) by including other progressions (I-IV-I for instance) and taking a look at other important composers like
   405|[...]
   406|Jean-Philippe Rameau (Bach’s contemporary composer), and (b) by using more
   407|[...]
   408|specific techniques of parsing, for instance we wish to use the new context-free
   409|[...]
   410|approach in [13] for producing new progressions from the grammars.
   411|- **[](https://ocw.mit.edu/courses/21m-380-music-and-technology-algorithmic-and-generative-music-spring-2010/2fe633842fb0fb4c2ee9572110d30fd1_MIT21M_380S10_assn_ss_c.pdf)**: Generative Context-free
   412|[...]
   413|For my final project, I made a system for defining context-free grammars (CFGs) and expanding
   414|a sequence of terminals into non-terminals using randomized productions. This contrasts with the
   415|[...]
   416|more common activity of parsing a string of already-generated terminals into higher-level nonterminals, which is essentially the reverse process. A sequence of terminals can then be processed
   417|[...]
   418|to produce some sort of output, such as a sound or a song. Using this system, complex definitions can be easily created as a series of productions, each of which maps a symbol to one or
   419|[...]
   420|more probabilistically-weighted expansions, in order to create complex randomized, but structured,
   421|[...]
   422|The generative CFG engine I created is essentially a tool for creating simple DSLs, with the
   423|added twist of randomized definitions. The random aspect makes the system suited for creative
   424|pursuits, such as music generation, that don’t require that a program always produce the same
   425|“correct” output (in fact, the opposite is often preferable when creating computer music), although
   426|a more conventional DSL can be specified by only specifying one expansion for each symbol. I hope
   427|the system can simplify the creation of complex musical generation by allowing users to create a
   428|generative CFG (or several) and then use it to quickly define and generate different types of music.
   429|[...]
   430|At the core of this system is the file gen.py. This script takes a string of space-delimited symbols
   431|and a grammar file, recursively expands each symbol according to the provided grammar until
   432|no more expansions are possible (i.e., none of the symbols have expansions in the grammar), and
   433|outputs the resulting sequence of terminals.
   434|[...]
   435|In general terms, a grammar in my system is a collection of productions, which map a symbol
   436|to one or more expansions. An expansion is a sequence of one or more symbols. Each expansion
   437|is annotated with a weight, reflecting how likely it is be chosen among the possible expansions.
   438|[...]
   439|Grammars in my system are represented as YAML files (YAML is a data serialization format known
   440|for its readability, see www.yaml.com). The grammar is represented as a hash, with symbols as
   441|keys and a hash of expansions as values. Each expansion hash is keyed by single space-delimited
   442|string of symbols (the expansion itself) with an integer weight as a value. As an example, here is a
   443|[...]
   444|grammar I made to represent a
   445|[...]
   446|, where the
   447|[...]
   448|is modeled as successive additions
   449|and multiplications of basic
   450|[...]
   451|Grammars can contain recursive productions, but each production must contain at least nonrecursive expansion since the script expands the sequence until it contains only terminals (symbols
   452|[...]
   453|without expansions).
   454|[...]
   455|is saved in sound.
   456|[...]
   457|As seen above, gen.py prints the sequence produced with successive expansions. The first line
   458|[...]
   459|is the initial
   460|[...]
   461|given, and the last
   462|[...]
   463|If we want to hear this song, we cannot use wavgen.py, as it combines waveforms into a
   464|single waveform through addition or multiplication instead of sequencing (we could play all the
   465|notes at the same time with wavgen.py though, as it automatically converts symbols of the form
   466|[...]
   467|“wav(freq)” to “+wav(freq)”). Instead we would like to sequence the notes. I wrote yet another
   468|[...]
   469|script, wavsequencer.py, to perform this task. wavsequencer.py takes an initial sequence and an
   470|arbitrary number of grammar files, like gen.py and wavgen.py, expands the initial sequence by
   471|composing the grammars, and then uses wavgen.py to render each symbol in the new sequence.
   472|[...]
   473|Of course, these are just examples of what can be produced. New grammars can be written
   474|to produce more complex songs and sounds by using the productions defined in sounds.yaml and
   475|notes.yaml, and new scripts can be written to interpret new types of symbols (or reinterpret existing
   476|symbols, as in the case of wavsequencer.py).
   477|[...]
   478|playing with the few
   479|- **[A Review Paper on Randomly Generating Music Using Context ...](https://easychair.org/publications/preprint/CCfg)**: A Review Paper on Randomly Generating Music Using Context-Free Grammars
   480|
   481|Download PDF Open PDF in browser
   482|
   483|# A Review Paper on Randomly Generating Music Using Context-Free Grammars
   484|
   485|### EasyChair Preprint 7674
   486|
   487|4 pages•
   488|
   489|Manomay Jamble, Kunal Jadhav, Aditya Jaybhay, Bhavesh Jagtap and Aniruddha Joshi
   490|
   491|### Abstract
   492|
   493|The paper hereby discusses the creation of a music sequence generator with the aim to assist artists in their integration. The series of steps is summarized to show the composition as nothing but a tabular flow of data refinement, and each and every step involved in the process is designed to benefit its functions. Majorly, the case of the main structure of the proposal is lexical grammar i.e. CFG method which was basically developed to produce random strings. A sequence of strings is then modelled to produce an output like a song.
   494|
   495|Keyphrases: Contextual grammar, harmonic progression, rhythm
   496|
   497|| Links: | https://easychair.org/publications/preprint/CCfg |
   498|| --- | --- |
   499|
   500|BibTeX entry
   501|

## 5. High-Complexity (ML/Transformers)
### High-Complexity (ML/Transformers)
- **[ISCA Archive - Exploring compressibility of transformer based text-to-music (TTM) models](https://www.isca-archive.org/interspeech_2024/moschopoulos24_interspeech.html)**: State-of-the art Text-To-Music (TTM) generative AI models are large and require desktop or server class compute, making them infeasible for deployment on mobile phones. This paper presents an analysis of trade-offs between model compression and generation performance of TTM models. We study compression through knowledge distillation and specific modifications that enable applicability over the various components of the TTM model (encoder, generative model and the decoder). Leveraging these methods we create TinyTTM (89.2M params) that achieves a FAD of 3.66 and KL of 1.32 on MusicBench dataset, better than MusicGen-Small (557.6M params) but not lower than MusicGen-small fine-tuned on MusicBench.
- **[](https://arxiv.org/pdf/2509.00914)**: The success of the generative model has gained unprecedented attention in the music generation area. Transformer-based architectures have set new benchmarks for
[...]
model performance. However, their practical adoption is hindered by some critical challenges: the demand for massive computational resources and inference
[...]
time, due to their large number of parameters. These obstacles make them infeasible to deploy on edge devices, such as smartphones and wearables, with limited
[...]
computational resources. In this work, we present TinyMusician, a lightweight
[...]
musical fidelity, computational cost, and deployment feasibility, especially for on-device deployment, where there are limited computational resources, such as smartphones and extended reality
[...]
glasses. The dependency on a cloud, with high computational overheads, hinders the proliferation of AI-generated products into real-world applications, such as games, and keeps these models
[...]
There are several efforts to reduce the model size, especially in Transformer architecture, such as
[...]
(2021)
[...]
used to reduce the computational cost of feed-forward layers of
[...]
Resource-Intensive Models: Transformer-based music models (Table 1) achieve remarkable quality but require strong GPU resources, which are incompatible with edge devices’ constraints; (2)
[...]
Recently, diffusion models (Song et al., 2020) and transformer-based models (Kang et al., 2024; Shih
[...]
Music-LM (Agostinelli et al., 2023) 3.44GB 860M RTX 3050 8GB
[...]
Transformer-based models (Wen et al., 2022), on the contrary, are experts in modeling long-range
[...]
positional encoding (Dash & Agres
[...]
024). However, the strong performance of transformer models
[...]
also comes with a tradeoff: the transformer-based architecture requires substantial computational
[...]
resources, which hinders their deployment on small battery-powered devices.
[...]
Since our approach is also transformer-based, the popular transformer models for music generation
[...]
are listed in Table 1. As shown in this Table, even small state-of-the-art models have a large number
[...]
of parameters. For example, Yue-7B (Yuan et al., 2025) has 7 billion parameters and demands
[...]
about 40GB of memory model storage. Even the smallest model, MusicGen-Small, demands 10GB
[...]
of GPU memory and an RTX 3080 GPU to achieve acceptable inference speeds. Large model
[...]
parameters and high computational costs require devices with very high memory and computing
[...]
These evidences show that directly deploying such models not only incurs significant resource costs
[...]
but also triggers long inference times. Additionally, deploying these models on edge devices such
[...]
as mobile phones is impossible due to their limited storage and computing resources.
[...]
In particular, we incorporate resource utilization metrics and accuracy-related established benchmarks. The resource utilization includes inference time (in seconds), GPU FLOPS, CPU utilization
[...]
(in percentage), Memory Usage (in GB), GPU Memory Usage (in GB), and model size (in gigabytes), which collectively characterize the models’ computational efficiency and resource requirements.
[...]
-7B
[...]
This hierarchy highlights a core insight: naive compression (Int-8) or scale (large models) fails to
[...]
generation, whereas our TinyMusician-MixedPrecision
[...]
In this study, we address the critical
[...]
resource-constrained edge devices, such as mobile phones, by
[...]
TinyMusician, a
[...]
lightweight framework that integrates knowledge distillation
[...]
adaptive mixed-precision quantization. By using MusicGen as a baseline, we propose a stage-mixed bidirectional KL Divergence
[...]
a dynamic temperature annealing strategy to enhance the performance of knowledge transfer between the teacher and student models
[...]
To further optimize
[...]
a 55% reduction in model size while preserving the performance. For
[...]
how to speed up
- **[Local deployment of large-scale music AI models on commodity hardware](https://arxiv.org/html/2411.09625v1)**: We present the MIDInfinite, a web application capable of generating symbolic music using a large-scale generative AI model locally on commodity hardware. Creating this demo involved porting the Anticipatory Music Transformer, a large language model (LLM
[...]
pre-trained on the Lakh MIDI dataset
[...]
to the Machine Learning Compilation (MLC
[...]
model is ported
[...]
MLC facilitates inference on a variety of runtimes
[...]
C++, mobile, and the browser
[...]
and technology more familiar to music software developers
[...]
a proof of concept
[...]
72.
[...]
% of generations,
[...]
.38
[...]
.38
[...]
.386.3% with 222
[...]
upfront buffering.
[...]
Large-scale generative AI models for music are increasingly capable and have the potential to transform both music technology and music creation workflows. However, models are currently tightly coupled to a software ecosystem (e.g., Python, CLIs) distinct from that of music technology (e.g., C++, VST plugins), inhibiting music software developers from exploring this potential. Moreover, models may even require specialized hardware (GPUs) for deployment, a prerequisite that may be unrealistic for many musicians. Cloud inference via APIs could help to bridge this gap [1], but music creativity benefits from low latency, high reliability, and increased privacy, all of which are difficult to guarantee with APIs.
[...]
In this work, we propose a realistic workflow for deploying large-scale generative AI models on device in the music technology ecosystem (Figure 1). This workflow involves decentralized contributions from two key groups of stakeholders: model providers and music software developers. Model providers are tasked with porting their models to the MLC-LLM framework [2, 3, 4, 5, 6], which facilitates both model compilation and provides a variety of runtimes for deployment. Accordingly, MLC-LLM helps overcome two key obstacles associated with the music technology ecosystem: model compilation allows models to run faster on commodity hardware, and runtimes bridge the gap to technology stacks more familiar to music software developers (C++, web, mobile). Music software developers are then tasked with creating rich interactive music applications that incorporate these generative capabilities. Finally, musicians can then explore these applications on commodity hardware such as laptops and mobile devices in familiar environments like DAWs. Related work [7] allows model providers to deploy to a specific music application—while our goal is to enable music software developers to explore deployment across a multitude of music applications.
[...]
We select the Anticipatory Music Transformer [8], a state-of-the-art symbolic music generation model pre-trained on the Lakh MIDI dataset [9], as our target model for deployment. This model is available in a small (128M) and medium (360M) variant, reusing the architecture and hyperparameters of the GPT-2 [10] small and medium configurations respectively. Each MIDI note is encoded as a triplet of tokens representing (i) onset time, (ii) note duration, and (iii) instrument and pitch. The model was trained on sequences of 1024102410241024 tokens: one start-of-sequence token followed by 1023102310231023 tokens (341341341341 MIDI note triplets).
[...]
To port this model to MLC, we define the GPT-2 model architecture in a computational graph in TVM’s Relax frontend, which is translated to Relax IR for optimization and compilation [6]. We also convert the original model weights into an MLC-compatible format. We release our ported model to Github and converted weights to Huggingface (links embedded in footer of demo page). Once the model is compiled, it can be deployed across various hardware platforms using MLC’s platform-native runtime. For instance, the runtime leverages WebGPU for browser-based execution and Metal for Apple devices to achieve efficient performance across platforms (Table 1).
- **[TinyMusician: On-Device Music Generation with Knowledge Distillation and Mixed Precision Quantization](https://www.emergentmind.com/papers/2509.00914)**: Abstract: The success of the generative model has gained unprecedented attention in the music generation area. Transformer-based architectures have set new benchmarks for model performance. However, their practical adoption is hindered by some critical challenges: the demand for massive computational resources and inference time, due to their large number of parameters. These obstacles make them infeasible to deploy on edge devices, such as smartphones and wearables, with limited computational resources. In this work, we present TinyMusician, a lightweight music generation model distilled from MusicGen (a State-of-the-art music generation model). TinyMusician integrates two innovations: (i) Stage-mixed Bidirectional and Skewed KL-Divergence and (ii) Adaptive Mixed-Precision Quantization. The experimental results demonstrate that TinyMusician retains 93% of the MusicGen-Small performance with 55% less model size. TinyMusician is the first mobile-deployable music generation model that eliminates cloud dependency while maintaining high audio fidelity and efficient resource usage
- **[TinyMusician: On-device Music Generation](https://www.emergentmind.com/topics/tinymusician)**: - TinyMusician is a lightweight on-device music generation model that employs advanced knowledge distillation and quantization to reduce model size by 55% while retaining 93% of the teacher's performance.
- It utilizes Stage-mixed Bidirectional and Skewed KL-Divergence for effective training and Adaptive Mixed-Precision Quantization to ensure efficient deployment on resource-constrained devices.
- The model demonstrates high audio fidelity with competitive FAD scores and robust text-audio alignment, enabling practical applications in mobile composition, game audio, and educational tools.
[...]
TinyMusician is a lightweight, mobile-deployable music generation model specifically designed to eliminate cloud dependency and enable high-fidelity automatic music creation directly on resource-constrained devices. The model is distilled from MusicGen-Small, a Transformer-based architecture, and incorporates two principal innovations to effectively compress the teacher’s capabilities: (i) Stage-mixed Bidirectional and Skewed Kullback–Leibler (KL) Divergence for advanced knowledge distillation, and (ii) Adaptive Mixed-Precision Quantization for efficient deployment and inference. TinyMusician achieves approximately 93% of the teacher’s performance while reducing model size by 55%, and is deployed fully on-device, setting a new standard for practical music generation accessibility.
[...]
TinyMusician utilizes a transformer backbone transferred via knowledge distillation from MusicGen-Small. The core innovation is the Stage-mixed Bidirectional and Skewed KL-Divergence, which dynamically guides the student model’s learning across different temporal segments of musical structure:
[...]
training, and
[...]
To achieve device-level efficiency, TinyMusician employs partitioned quantization strategies:
[...]
- Text Encoder (T5): Int8 precision for embedding efficiency, maintaining core sequence semantics.
- MusicGen-Decoder: Float16 precision for stability in autoregressive music token generation.
- Encodec-Decoder: Full Float32 precision to retain reconstruction fidelity in audio waveform synthesis.
[...]
The entire PyTorch model is converted to ONNX via Optimum-Cli, optimizing for native mixed precision and fast inference.
[...]
- Model size reduced from 3.2GB (baseline) to 1.04GB (quantized).
- Inference times measured at 26.54 seconds (quantized) versus 10 seconds (full precision).
- Lowered GPU/CPU memory requirements facilitate deployment on edge devices.
[...]
Runs efficiently and natively on consumer devices (e.g., iPhone 16 Pro, iOS 18.2) using hardware-optimized kernels.
[...]
Quantization yields substantial reductions in memory and model size, though at the cost of increased latency and, in some configurations, a ~9.5% reduction in melodic/harmonic fidelity.

- Training Dynamics:
[...]
- Quantized kernel optimization requires further development to fully leverage hardware for lower latency.
- Minor reductions in text-to-audio alignment associated with aggressive compression.
- Future extension to larger, multi-modal music generation models and further exploration of the precision-performance frontier is required.
[...]
- Kernel and runtime optimizations to further minimize inference latency in quantized mode.
- Integration with multimodal or cross-domain models (text+image+music) and expansion to larger MusicGen variants.
- Granular quantization schedules and compression strategies to target optimal trade-offs between resource consumption and audio fidelity.
- Broader benchmarking across genres and complex musical tasks, addressing harmonic richness, stylistic nuance, and polyphony.
[...]
TinyMusician establishes a device-local paradigm for generative music models, grounded in advanced distillation and quantization principles. Its empirically validated performance and efficient architecture position it as a reference model for embedded, resource-aware creative AI in music generation (Wang et al., 31 Aug 2025).

## 6. Initial Library Audit Findings
- **[muspy v0.5.0](https://pypi.org/project/muspy/)**: MusPy is an open source Python library for symbolic music generation. It provides essential tools for developing a music generation system, including dataset management, data I/O, data preprocessing and model evaluation.
[...]
- Dataset management system for commonly used datasets with interfaces to PyTorch and TensorFlow.
- Data I/O for common symbolic music formats (e.g., MIDI, MusicXML and ABC) and interfaces to other symbolic music libraries (e.g., music21, mido, pretty_midi and Pypianoroll).
- Implementations of common music representations for music generation, including the pitch-based, the event-based, the piano-roll and the note-based representations.
- Model evaluation tools for music generation systems, including audio rendering, score and piano-roll visualizations and objective metrics.
[...]
| Package | Constraint |
| --- | --- |
| PyYAML | >=3.0 |
| bidict | >=0.21 |
| joblib | >=0.15 |
| matplotlib | >=1.5 |
| miditoolkit | >=0.1 |
| mido | >=1.0 |
| music21 | >=6.0 |
| pretty-midi | >=0.2 |
| pypianoroll | >=1.0 |
| requests | >=2.0 |
| tqdm | >=4.0 |
[...]
| Package | Constraint |
| --- | --- |
| black | >=19.0 |
| flake8-docstrings | >=1.5 |
| flake8 | >=3.8 |
| mypy | >=0.900 |
| pylint | >=2.5 |
| sphinx-rtd-theme | >=0.5 |
| sphinx | >=3.0 |
| tensorflow | >=2.0 |
| torch | >=1.0 |
| jsonschema | >=3.0 |
| xmlschema | >=1.0 |
| yamale | >=2.0 |
| pytest | >=6.0 |
| pytest-cov | >=2.0 |
- **[Installing](http://mido.readthedocs.io/en/latest/installing.html)**: Mido requires Python version 3.7 or higher.
[...]
A few dependencies are also required in order to allow Mido to introspect its own version:
[...]
- packaging
- importlib_metadata for Python < 3.8
[...]
Dependency management is handled automatically when installing using the recommended methods. No need to bother installing these manually.
[...]
Dependencies for the loaded on-demand port backend(s) are optional unless you want to use the ports feature.
[...]
The recommended installation method is to use pip to retrieve the package from PyPi.
[...]
Consider using a virtual environment to isolate your installation from your current environment.
[...]
This ensures that you always get the latest released stable version:

python3 -m pip install mido
[...]
Or, alternatively, if you want to use ports with the default backend:

python3 -m pip install mido[ports-rtmidi]
- **[mido/mido](https://github.com/mido/mido)**: MIDI Objects for
[...]
Mido is a library for working with MIDI messages and ports:
[...]
- convenient message objects.

- supports RtMidi, PortMidi and Pygame. New backends are easy to

write.
[...]
- includes a reusable MIDI stream parser.
[...]
Mido requires Python 3.7 or higher.
[...]
::

python3 -m pip install mido
[...]
Or, alternatively, if you want to use ports with the default backend::

python3 -m pip install mido[ports-rtmidi]
[...]
See ``docs/backends/`` for other backends.
- **[SciPy - PyPI](https://pypi.org/project/scipy/)**: SciPy (pronounced "Sigh Pie") is an open-source software for mathematics,
science, and engineering. It includes modules for statistics, optimization,
integration, linear algebra, Fourier transforms, signal and image processing,
ODE solvers, and more.
[...]
SciPy is built to work with
NumPy arrays, and provides many user-friendly and efficient numerical routines,
such as routines for numerical integration and optimization. Together, they
run on all popular operating systems, are quick to install, and are free of
charge. NumPy and SciPy are easy to use, but powerful enough to be depended
upon by some of the world's leading scientists and engineers. If you need to
manipulate numbers on a computer and display or publish the results, give
SciPy a try!
[...]
| Package | Constraint |
| --- | --- |
| numpy | <2.7,>=1.26.4 |
[...]
## Optional Dependencies
[...]
| Package | Constraint |
| --- | --- |
| pytest | >=8.0.0 |
| pytest-cov | |
| pytest-timeout | |
| pytest-xdist | |
| asv | |
| mpmath | |
| gmpy2 | |
| threadpoolctl | |
| scikit-umfpack | |
| pooch | |
| hypothesis | >=6.30 |
| array-api-strict | >=2.3.1 |
| Cython | |
| meson | |
| ninja | |
| sphinx | <8.2.0,>=5.0.0 |
| intersphinx_registry | |
| pydata-sphinx-theme | >=0.15.2 |
| sphinx-copybutton | |
| sphinx-design | >=0.4.0 |
| matplotlib | >=3.5 |
| numpydoc | |
| jupytext | |
| myst-nb | >=1.2.0 |
| pooch | |
| jupyterlite-sphinx | >=0.19.1 |
| jupyterlite-pyodide-kernel | |
| linkify-it-py | |
| tabulate | |
| click | <8.3.0 |
| spin | |
| mypy | ==1.10.0 |
| typing_extensions | |
| types-psutil | |
| pycodestyle | |
| ruff | >=0.12.0 |
| cython-lint | >=0.12.2 |
- **[Statistical functions (scipy.stats) — SciPy v1.17.0 Manual](https://docs.scipy.org/doc/scipy/reference/stats.html)**: Statistical functions (scipy.stats) — SciPy v1.17.0 Manual
[...]
# Statistical functions (scipy.stats)#
[...]
This module contains a large number of probability distributions, summary and frequency statistics, correlation functions and statistical tests, masked statistics, kernel density estimation, quasi-Monte Carlo functionality, and more.
[...]
Statistics is a very large area, and there are topics that are out of scope for SciPy and are covered by other packages. Some of the most important ones are:
[...]
statsmodels: regression, linear models, time series analysis, extensions to topics also covered by`scipy.stats`.
[...]
rpy2: Python to R bridge.
[...]
An overview of statistical functions is given below. Many of these functions have a similar version in scipy.stats.mstats which work for masked arrays.
[...]
## Masked statistics functions#
[...]
Statistical functions for masked arrays (scipy.stats.mstats)
[...]
### Warnings / Errors used in scipy.stats#
[...]
### Result classes used in scipy.stats#
[...]
These classes are private, but they are included here because instances of them are returned by other statistical functions. User import and instantiation is not supported.
