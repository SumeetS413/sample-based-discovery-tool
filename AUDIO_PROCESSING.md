# Audion Preprocessing and Normalization

At a high level: You take the raw selected audio clip and convert it into a clean, standardized, comparable version before generating a fingerprint.

Think of it like preparing data before running a machine learning model. You’re trying to remove irrelevant differences so that only the musically meaningful content remains.

Two clips that are basically the same sound can look very different digitally.

For example:
- one is loud, one is quiet
- one is stereo, one is mono
- one is `44.1kHz`, one is `48kHz`
- one has silence at the start
- one has background noise

Without normalization, the system might think: “These are completely different”, even if they are actually the same sample. So preprocessing is about making the comparison fair.

Here’s the typical sequence: 
`Raw Clip
→ Decode
→ Resample
→ Mono
→ Normalize
→ Trim
→ Final Clip`

## Sequence:

### 1. Decode the audio file
What’s happening: Your uploaded file (`.mp3`, `.wav`, etc.) is compressed or encoded. You need to convert it into raw audio data.

Output becomes: a waveform (array of amplitudes over time)

Why this matters: Fingerprinting and analysis work on raw audio, not compressed formats.

### 2. Resample to a standard sample rate
Sample rate is how many samples per second are stored.

Examples:
- `44,100 Hz` (CD quality)
- `48,000 Hz` (common in production)

What you do: Convert everything to a single standard, e.g.:

$$
f_s = 22050 \text{ Hz (or 44100 Hz)}
$$

Why this matters: 
If two clips have different sample rates:
- Their data representations differ  
- Timing alignment can be off  

Resampling ensures 1 second of audio is same number of samples everywhere.

### 3. Convert to Mono

Stereo audio has two channels:
- Left  
- Right  

Mono uses one combined channel.

What you do:

$$
\text{mono}(t) = \frac{L(t) + R(t)}{2}
$$

Why this matters:
- Samples are usually about content, not spatial placement  
- Stereo differences are often irrelevant noise for matching  

Without this, left/right differences could confuse similarity detection.

### 4. Normalize Amplitude (Loudness)

Two identical sounds can have different volumes.

Example:
- Clip A peaks at `0.9`  
- Clip B peaks at `0.3`  

They are the same pattern, but scaled.

What you do:
Scale amplitudes so they fall into a consistent range.

$$
x_{\text{normalized}} = \frac{x}{\max(|x|)}
$$

Why this matters: You want to compare **shape**, not volume.

Otherwise:
- Loud clips dominate  
- Quiet clips get ignored  

### 5. Trim Silence / Noise

There may be a problem when users may select a segment with:
- Silence at the beginning  
- Dead space at the end  

What you do: Remove low-energy sections:
- detect near-zero amplitude regions
- trim them off

Why this matters: Fingerprinting works best on actual signal, not empty space.

### 6. **(Optional)** Band Filtering

Focus on frequency ranges where samples are strongest:
- mid frequencies (melody, vocals)
- harmonic content

Samples are often:
- filtered
- EQ’d
- altered

So focusing on stable frequency bands can improve matching.

### 7. Output

At the end, you now have:
- fixed sample rate
- mono signal
- normalized amplitude
- trimmed content

This is your analysis-ready audio.