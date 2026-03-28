# CLAUDE.md — Strudel Algorave Workspace

## Permissions

- You are allowed to search the internet for anything (web search, fetching URLs, downloading files).

## Classical Music Scores

**Source:** [IMSLP (International Music Score Library Project)](https://imslp.org/) — public domain scores for classical music.
Example: [Saint-Saëns Cello Concerto No.2, Op.119](https://imslp.org/wiki/Cello_Concerto_No.2,_Op.119_(Saint-Sa%C3%ABns,_Camille))

**Workflow for transcriptions:**
1. Claude finds the IMSLP page and identifies the best score files (cello/piano reductions preferred over full orchestral scores)
2. User downloads the PDFs into `./files/`
3. Claude reads the PDFs and transcribes the notes into Strudel patterns

## Project Overview

Algorave music workspace using [Strudel](https://strudel.cc/) — a browser-based live coding environment for algorithmic music (JavaScript port of TidalCycles).

**Workflow:** Edit `.strudel` files in Neovim via strudel.nvim, which syncs to a Chromium browser running the Strudel REPL. Alternatively, paste code into https://strudel.cc/.

## Neovim + strudel.nvim Commands

**Keybindings (leader = Space):**
- `<Space>sl` — `:StrudelLaunch` — Launch Strudel (opens Chromium browser session)
- `<Space>st` — `:StrudelToggle` — Toggle play/pause
- `<Space>su` — `:StrudelUpdate` — Evaluate/send current buffer to Strudel
- `<Space>ss` — `:StrudelStop` — Stop playback
- `<Space>sq` — `:StrudelQuit` — Quit Strudel (close browser)
- `<Space>sb` — `:StrudelSetBuffer` — Switch synced buffer
- `<Space>sx` — `:StrudelExecute` — Switch buffer and evaluate

**Workflow:** `<Space>sl` once to launch, edit code, `:w` to save (auto-evaluates with `update_on_save = true`). `<Space>st` to pause/resume.

**Useful Neovim commands:**
- `:%s/old/new/g` — search and replace across the whole file (e.g. `:%s/slow(25)/slow(12)/g`)
- `/pattern` — search only (does not replace)
- `:q!` — quit without saving
- `:qa!` — quit all buffers without saving

## Composition Best Practices (Learned)

### Tempo & Pacing
- **Always use `arrange()` for multi-section pieces** — `cat()` alternates patterns per cycle, it does NOT play them sequentially to completion.
- **Always pair `arrange([N, ...])` with `.slow(N)`** — the arrange cycle count and slow value must match, otherwise the tempo is wrong. Example: `[5.5, note(...).slow(5.5)]`
- **Granular tempo variation is essential** — split into individual measures or 2-measure phrases. The opening of a piece should be the most granular (measure-by-measure) to nail the feel in the first 10-20 seconds.
- **Tempo calibration (mellotron cello, per measure):** `slow(1.5-2)` = moderate single measure. `slow(1.3-1.5)` = fast/driving. `slow(2-2.5)` = broad/held. For 2-measure groups, double these values.
- **Tempo shape matters:** slightly held opening → flowing middle → push forward on tension → broaden on resolution → slow dramatically at end.
- **Never use one uniform `slow()` for an entire piece** — it sounds mechanical and wrong.

### Harmony & Notes
- **Stay diatonic by default.** Only use accidentals (sharps/flats outside the key) when harmonically justified. A wrong accidental sounds far worse than a slightly wrong diatonic note.
- **G major uses: G A B C D E F#.** No Bb, no C#. C# only appears in dominant D major cadential measures.
- **D minor uses: D E F G A Bb C (natural) or C# (leading tone).** C# only in dominant A major context.
- **E minor uses: E F# G A B C D.** D# only as leading tone in dominant B major context.
- **When reading scores and unsure of a note, choose the diatonic option.** It will always sound better than a wrong accidental.
- **Verify every accidental:** scan the entire file for sharps/flats and ask "does this note belong in the key?" Remove any that don't have clear harmonic justification.

### Articulation
- **Do NOT use `.cut(1)` on solo melody lines** — it kills note overlap and sounds choppy. Real instruments have legato.
- **Keep `.cut(N)` on accompaniment** (strings, brass, bass) where separation is appropriate.
- **Solo cello runs:** `.attack(0.005).release(0.1-0.15)` — crisp but with slight natural overlap from no `.cut()`.
- **Held notes:** `.attack(0.02).release(0.4-0.6)` — allows sustained notes to breathe.
- **Open reverb for endings:** increase `.room()` and `.roomsize()` in final sections.

### Dynamic Shape
- Vary `.gain()` across sections: quieter for intimate passages (0.75-0.78), louder for climaxes (0.82-0.85).
- Held notes and breathing pauses between phrases make music sound human.

### Working with Mellotron Samples
- Use `note()` with explicit pitch names (e.g. `note("d4 e4 f4")`), NOT `n()` with `.scale()` — scale degrees don't work with mellotron samples.
- Keep `.cut(N)` on accompaniment instruments, but NOT on solo melody lines.

### Score Transcription Workflow
- MuseScore screenshots (clean computer-engraved) are much easier to read than scanned 19th-century PDFs.
- Guitar tabs can be used but require transposition (and octave rearrangement).
- After transcribing, always do an accidental audit: check every # and b against the key signature.
- **Use MIDI-to-Strudel converter** (https://emanuel-de-jong.github.io/MIDI-To-Strudel/) for accurate note data when available.

### Naming & Presentation
- "Kaya" pieces in root folder have all identifying comments stripped — only `// Kaya Minor 1` etc.
- Working/reference versions live in `songs/` with full attribution.
- The `audit-classical-strudel` skill (~/.claude/skills/) provides a systematic checklist for reviewing pieces.

### Key Lesson: Match Real Recordings
- Always compare against a specific recording (user provides Spotify links).
- The first 10-20 seconds must be convincing — make the opening the most granular.
- Listeners notice wrong speed and wrong notes more than anything else. Get tempo and key right first, then refine articulation.

## File Conventions

- Song files: `*.strudel` (JavaScript/Strudel pattern code)
- Each file is a self-contained composition that can be evaluated in the Strudel REPL

## External Resources (from awesome-strudel)

### MIDI to Strudel Converter
Convert MIDI files to Strudel code: https://github.com/Emanuel-de-Jong/MIDI-To-Strudel
- Web version: https://emanuel-de-jong.github.io/MIDI-To-Strudel/
- Python CLI: `python Midi-to-Strudel.py -m file.mid -n 16`
- Outputs `note()` patterns with `setcpm()` tempo — great for getting accurate notes from existing MIDI files
- Only supports 4/4 time

### Song Collection for Reference
https://github.com/eefano/strudel-songs-collection — 81 files covering pop covers, electronic, waltz, experimental. Good for studying patterns.

### Sample Pack Discovery
- Browse packs: https://strudel-samples.alternet.site
- Load any GitHub pack: `samples('github:username/repo')`
- Notable packs: `tidalcycles/Dirt-Samples` (comprehensive), `yaxu/clean-breaks` (breakbeats), `Bubobubobubobubo/Dough-Amen` (amen breaks)

### Tutorials
- Official workshop: https://strudel.cc/workshop/getting-started/
- Music production course: https://github.com/terryds/learning-music-production-with-strudel

## Custom Samples

### Mellotron (Cello, Strings, Brass, etc.)
```javascript
samples('https://sound.intercrap.com/strudel/mellotron/strudel.json')
```
Available instruments: `Cello`, `M300A` (Two Violins), `M300B` (Solo Violin), `MkIIViolins`, `StringSection`, `GC3Brass`, `MkIICombinedBrass`, `Trombone`, `Trumpet`, `MkIIFlute`, `Bassoon`, `TenorSax`, `8Choir`, `CombinedChoir`, `Orchestra`, `Vibes`, `MkIIChurchOrgan`, `StJohnsOrgan`, `ItalianAccordion`

Use `.cut(1)` on accompaniment mellotron samples (they're 7-8 seconds long). Do NOT use `.cut()` on solo melody lines.

### Drum Machine Banks (built-in)
Use with `.bank("name")`: `RolandTR808`, `RolandTR909`, `linn` (LinnDrum), `dmx` (Oberheim DMX), `RolandCompuRhythm1000`
```javascript
s("bd sd [~ bd] sd, hh*8").bank("RolandTR808")
```

### GM Synth Sounds (built-in)
Use with `.s("name")`: `gm_synth_bass_1`, `gm_lead_2_sawtooth`, `gm_epiano1`, `gm_strings`, etc.

## Genre Patterns

### Techno/Electronic
```javascript
// Use $: syntax for independent concurrent patterns (mute with //$: or _$:)
setCps(140/60/4) // BPM conversion
// Acid envelope — register custom function:
register('acidenv', (x, pat) => pat.lpf(100).lpenv(x * 9).lps(.2).lpd(.12))
```
- **Drum machines:** `.bank("RolandTR909")`, `.bank("RolandTR808")`, `.bank("linn")`, `.bank("dmx")`
- **Sidechain ducking:** `.duck("2:3:4").duckattack(.2).duckdepth(.8)` on the kick
- **Random notes:** `irand(N)` + `.seg(16)` to quantize to 16ths
- **Effect buses:** `.orbit(N)` to route patterns separately
- **Live muting:** `$:` patterns can be muted with `//` or `_` prefix, save to apply

### Jazz Essentials
- **Real samples:** `piano1`, `fmpiano`, `sax`, `sax_vib`, `sax_stacc`, `vibraphone`
- **Jazz drums:** `samples('github:tidalcycles/dirt-samples')` → `jazz:0` (kick), `jazz:1` (snare), `jazz:5` (ride)
- **Upright bass:** `sawtooth` with `.lpf(400-500).lpq(2)` (no real sample available)
- `chord().voicing()` for jazz harmony, `.swingBy(1/6, 4)` for swing

### Synthwave Essentials
- **Supersaw pad:** `.superimpose(x => x.add(0.08))` twice (+ and -) on sawtooth
- **Arps:** 16th-note cascades with `.delay(.25).delaytime(3/16).delayfeedback(.45)`
- **Gated snare:** `.room(0.7).roomsize(3)` on TR-909 snare
- **Filter sweep:** `.lpf(sine.range(800, 5000).slow(16))`
- **FM synthesis:** `.fm("2").fmh(2.04)` for metallic tones

### Visualizations (always add to electronic pieces)
- `._pianoroll()` — note grid (melodic patterns)
- `._scope()` — waveform (kicks/bass)
- `._spectrum({color:"#00ffff", thickness:2})` — frequency analyzer
- Global `.pianoroll()` (no `_`) may not work via strudel.nvim

---

## Strudel Syntax Reference

### Core Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `s()` | Play sample or synth | `s("bd sd hh")` |
| `note()` | Set pitch (letter/MIDI) | `note("c3 e3 g3")` |
| `freq()` | Set pitch by frequency | `freq("220 440")` |
| `n()` | Select sample variant or scale degree | `n("0 1 2 3")` |
| `stack()` | Layer patterns simultaneously | `stack(s("bd"), s("hh*4"))` |
| `cat()` | Concatenate patterns (1 cycle each) | `cat("c3", "e3").note()` |
| `seq()` | Concatenate squeezed into 1 cycle | `seq("c3", "e3").note()` |
| `bank()` | Select drum machine bank | `s("bd").bank("RolandTR808")` |
| `samples()` | Load sample pack from URL | `samples('url/strudel.json')` |

### Mini-Notation (Inside Quoted Strings)

| Operator | Meaning | Example |
|----------|---------|---------|
| `space` | Sequence events | `"c d e g"` |
| `*` | Speed up / multiply | `"hh*8"` |
| `/` | Slow down / divide | `"c d e g"/2` |
| `[]` | Group / subdivide | `"c [d e] g"` |
| `<>` | Alternate per cycle | `"<c e g>"` |
| `,` | Play simultaneously | `"[c3,e3,g3]"` |
| `~` | Rest / silence | `"bd ~ sd ~"` |
| `!` | Replicate (no speedup) | `"bd!4"` |
| `@` | Elongate (weight) | `"c@3 d"` |
| `?` | Random drop (50% default) | `"hh*8?"` |
| `\|` | Random choice | `"bd\|sd"` |
| `(k,n)` | Euclidean rhythm | `"bd(3,8)"` |
| `(k,n,r)` | Euclidean + rotation | `"bd(3,8,2)"` |

### Synths / Oscillators

Set with `.s()`: `sine`, `sawtooth`, `square`, `triangle`

```javascript
note("c3 e3 g3").s("sawtooth")
```

Noise: `white`, `pink`, `brown`

### Effects

**Filters:**
- `lpf(freq)` / `cutoff` — lowpass filter
- `hpf(freq)` — highpass filter
- `bpf(freq)` — bandpass filter
- `lpq(q)` / `resonance` — filter resonance
- `vowel("a e i o u")` — vowel filter

**Envelope (ADSR):**
- `attack(s)`, `decay(s)`, `sustain(level)`, `release(s)`
- `adsr(".1:.1:.5:.2")` — shorthand

**Filter Envelope:**
- `lpenv(depth)`, `lpattack`, `lpdecay`, `lpsustain`, `lprelease`

**Dynamics:**
- `gain(amount)` — volume (exponential)
- `velocity(0-1)` — multiplied with gain

**Reverb:**
- `room(0-1)` — reverb amount
- `roomsize(size)`, `roomlp(freq)`, `roomdim(freq)`

**Delay:**
- `delay(0-1)` — delay amount
- `delaytime(time)`, `delayfeedback(amount)`

**Distortion:**
- `distort(amount)`, `crush(bits)`, `coarse(factor)`
- `shape(amount)` — waveshaping

**Stereo:**
- `pan(0-1)` — left to right
- `jux(fn)` — apply function to right channel only

**Modulation:**
- `phaser(speed)`, `phaserdepth(depth)`
- `vib(hz)` / `vibmod(semitones)` — vibrato

**Sidechain:**
- `duckorbit(n)`, `duckattack(time)`, `duckdepth(0-1)`

### Tonal Functions

**Scales:**
```javascript
n("0 2 4 6").scale("C:minor")        // Scale degrees to notes
n("0 2 4 6").scale("C4:dorian")      // With octave
```

**Chords + Voicings:**
```javascript
chord("<Am7 Dm7 G7 C^7>").voicing()  // Auto voice leading
n("0 1 2 3").chord("<C Am F G>").voicing()  // Arpeggiate voicings
```

**Root Notes:**
```javascript
"<C^7 Dm7 G7>".rootNotes(2).note()   // Bass from chord symbols
```

**Transpose:**
- `transpose(semitones)` — chromatic shift
- `scaleTranspose(steps)` — shift within scale

### Time & Pattern Modifiers

**Speed:**
- `slow(n)` — stretch over n cycles
- `fast(n)` — compress into 1/n cycle
- `rev()` — reverse pattern
- `palindrome()` — forward then backward

**Offset:**
- `early(cycles)`, `late(cycles)` — shift in time
- `off(time, fn)` — delayed superimposition

**Euclidean:**
- `euclid(pulses, steps)` — euclidean rhythm
- `euclidRot(pulses, steps, rotation)`

**Structure:**
- `iter(n)` — rotate subdivisions each cycle
- `ply(n)` — repeat each event n times
- `segment(n)` — quantize to n events per cycle
- `swingBy(amount, subdivision)` — shuffle feel

### Conditional / Probabilistic

- `every(n, fn)` — apply fn every n cycles
- `sometimes(fn)` — 50% probability
- `sometimesBy(p, fn)` — custom probability
- `rarely(fn)` / `often(fn)` / `almostAlways(fn)`
- `degradeBy(amount)` — randomly remove events
- `when(pattern, fn)` — conditional on binary pattern
- `choose(a, b, c)` — random selection
- `wchoose([a, weight], ...)` — weighted random

### Accumulation / Layering

- `superimpose(fn)` — layer fn result on top of original
- `layer(fn)` — like superimpose but without original
- `off(time, fn)` — superimpose with time offset
- `echo(times, time, feedback)` — repeating echoes
- `jux(fn)` — apply fn to right channel

### Value Modifiers

- `add(n)`, `sub(n)`, `mul(n)`, `div(n)` — arithmetic
- `range(min, max)` — scale 0-1 to range
- `rangex(min, max)` — exponential scaling

### Signal Generators (for modulation)

```javascript
sine, cosine, saw, square, tri, rand, perlin
// Use with .range():
note("c3").lpf(sine.range(200, 4000).slow(4))
```

### Sample Playback

- `begin(0-1)` / `end(0-1)` — trim sample
- `speed(n)` — playback speed (negative = reverse)
- `loop(1)` — enable looping
- `loopBegin()` / `loopEnd()` — loop region
- `cut(group)` — cut group (stop overlapping samples)
- `clip(n)` / `legato(n)` — multiply duration
- `chop(n)` — granular slicing
- `slice(n, pattern)` — trigger specific slices
- `loopAt(cycles)` / `fit(cycles)` — fit to cycle length

### Composition Structure Example

```javascript
// Multi-part composition using stack
samples('https://sound.intercrap.com/strudel/mellotron/strudel.json')

stack(
  // Melody
  note("d4 f4 a4 g4").s("Cello").cut(1)
    .slow(2).room(0.3),

  // Chords
  chord("<Dm7 Gm7 C7 F^7>").voicing()
    .s("sine").decay(0.4).room(0.5),

  // Bass
  "<Dm Gm C F>".rootNotes(2).note()
    .s("sawtooth").lpf(400).decay(0.3),

  // Drums
  s("bd [~ sd] bd [sd ~], hh*8")
    .gain(0.8)
)
```
