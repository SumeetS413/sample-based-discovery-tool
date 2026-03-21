# System Architecture

## High Level Design:

The system can be divided into five major segments:
- Client UI Layer
- App/API Layer
- Audio Processing Layer
- Matching Layer
- Metadata / Storage Layer

A simplified chain looks like this:
`Client UI → App/API Layer → Audio Processing Layer → Matching Layer → Metadata / Storage Layer`

## Layers Explained:

### Client UI Layer

#### Recommended Stack:
- Next.js
- TypeScript
- Tailwind CSS
- WaveSurfer.js (for waveform rendering and segment regions)

#### Responsibilities:

This layer handles:
- upload interface
- waveform visualization
- playback controls
- segment selection
- results display
- comparison player
- outbound exploration links

#### What lives here:
- Upload panel
- Waveform viewer
- Segment selector
- Analyze action
- Match result cards
- Audio comparison player
- Crossfade / blend interface
Why this stack is recommended

### App/API Layer

#### Recommended Stack:
- FastAPI for the core backend API
- Next.js route handlers only for light frontend-side app logic if needed

#### Responsibilities:

This layer handles:
- receiving analysis requests
- file validation and orchestration
- timestamp validation
- passing work into the audio-processing pipeline
- returning results to the frontend

#### What lives here:
- upload endpoints
- analyze-segment endpoints
- job orchestration
- response formatting

### Audio Processing Layer

#### Recommended Stack:
- Python
- FFmpeg for segment extraction and media transformation
- helper libraries for audio loading and manipulation as needed

#### Responsibilities:
This layer handles:
- segment extraction from the full track
- format conversion
- resampling
- mono conversion
- loudness normalization
- optional silence trimming

#### What lives here:
- segment extractor
- audio normalizer
- preprocessing utilities

#### Alternative paths / decisions still open:
- Librosa can be used for analysis-oriented audio handling
- PyDub can be used for simpler clip manipulation
- (a more advanced DSP stack could be introduced later if the project expands into ML-based feature extraction)

### Matching Layer

#### Recommended Path:
- Fingerprint generation using an acoustic fingerprint approach
- Querying an external reference source (like AcoustID)

#### Responsibilities
This layer handles:
- converting clips into searchable representations
- querying a reference corpus
- retrieving candidate matches
- scoring and ranking results

#### What lives here
- fingerprint service
- match service
- ranking service

### Metadata / Storage Layer

#### Recommended Stack:
- PostgreSQL for structured metadata
- Object storage for uploaded audio files (S3-compatible storage)

#### Responsibilities:
This layer handles:
- uploaded file records
- analysis jobs
- selected segment metadata
- match history
- candidate result metadata
- optional outbound music catalog linkage

#### What lives here:
- uploads table
- segments table
- analysis jobs table
- matches table
