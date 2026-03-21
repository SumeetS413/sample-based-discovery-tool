# Full Pipeline Model

## Pipeline Overview:

There are really two major perspectives on the pipeline:
1. User/UI pipeline — what the user experiences
2. Data/music backend pipeline — what the system computes

A combined view is:
`Song Input
→ Waveform Interaction
→ Segment Selection
→ Analysis Request
→ Clip Extraction
→ Preprocessing
→ Fingerprint Generation
→ Match Lookup
→ Ranking
→ Metadata Enrichment
→ Result Display
→ Comparison / Exploration`

## Complete Example Pipeline:

Assume the user uploads a song file called `track.mp3`. While listening, they hear a short piano loop around `1:12.5` that sounds familiar and select the segment from `1:12.5` to `1:18.8`.

We now walk through the full process in detail.

### Step 1: Song Input

The user uploads `track.mp3`.

What the system has:
- the uploaded file
- file name and metadata
- a file ID

What the system does:
- stores the file
- validates it
- prepares it for UI playback and backend processing

Why it matters: This is the root object that all downstream processing depends on.

### Step 2: Waveform Rendering

The frontend renders the waveform of the uploaded file.

What the system has:
- the song file
- audio amplitude data for visualization

What the system does:
- displays the track as an interactive waveform
- allows playback scrubbing and inspection

Why it matters: It enables human-driven selection rather than blind automatic analysis. The song becomes something the user can inspect and target precisely.

### Step 3: Segment Selection

The user highlights the range from `72.5s` to `78.8s`.

What the system has:
- file ID
- start time
- end time

What the system does:
- stores the selected region
- prepares this region for analysis

Why it matters: Sample recognition is usually about a moment, not an entire recording. The system now narrows the question from “what is this song?” to “what does this specific clip resemble?”

### Step 4: Analysis Request

The frontend sends the selected region to the backend.

What the system has:

A request similar to:

```
{
  "file_id": "track_184",
  "start_time": 72.5,
  "end_time": 78.8
}
```

What the system does:
- validates the timestamps
- locates the stored file
- starts the backend processing workflow

Why it matters: This is the transition point between the UI layer and the audio-analysis layer.

### Step 5: Segment Extraction

The backend uses the stored file and timestamps to cut out the selected clip.

Technologies involved:
- FastAPI receives the request
- FFmpeg extracts the clip

What the system has now:
- a temporary 6.3-second clip containing only the selected region

Why it matters: This makes the analysis focused, efficient, and relevant. The full song is reduced to the exact material the user cares about.

### Step 6: Audio Preprocessing / Normalization

The backend standardizes the selected clip.

Technologies involved:
- FFmpeg and Python processing utilities (specifics determined later in planning)

What the system may do:
- decode audio
- resample to a consistent rate
- convert stereo to mono
- normalize amplitude
- trim silence if appropriate

What the system has now:
- a clean, standardized analysis-ready clip

Why it matters: Two similar musical clips may look very different digitally if their formats or volume differ. Preprocessing improves matching reliability. The system removes irrelevant technical variation so it can compare clips fairly.

### Step 7: Fingerprint Generation

The backend converts the cleaned clip into a machine-comparable representation.

Technologies involved:
- fingerprint service (specifics determined later in planning, possibly connected to the candidate corpus)
- audio-processing utilities (specifics determined later in planning)

What the system has now:
- a compact fingerprint or comparable representation of the selected segment

Why it matters: The computer needs a representation it can compare efficiently against a reference set. The clip is transformed into a searchable signature.

### Step 8: Match Lookup

The backend sends the representation into the matching layer.

Technologies involved:
- external fingerprint lookup/reference library 
- match service

What the system has access to:
- the selected clip fingerprint
- a candidate reference corpus

What the system does:
- queries the reference source
- retrieves candidate songs that look similar

Why it matters: This is where the project actually attempts to find the possible original sample. The system asks: “Which known songs are most likely related to this selected clip?”

### Step 9: Ranking

The backend receives raw candidate results and sorts them.

Technologies involved
- ranking service

What the system does:
- scores the results
- orders them by match quality
- filters weak or noisy candidates when necessary

Ranking might consider:
- fingerprint similarity score
- confidence score from the lookup service
- metadata quality
- track popularity or reliability

Why it matters: Strong ranking is essential for good user trust and product quality. The system turns raw retrieval into a usable answer set.

### Step 10: Metadata Enrichment

The backend enriches the ranked candidates with human-readable metadata.

Technologies involved:
- metadata service (possibly pulled from candidate corpus)
- database
- optional outbound catalog lookup

What the system adds:
- title
- artist
- album
- artwork
- external listening links

Why it matters: Users need understandable context, not just an internal match score. Technical matches become explorable music results.

### Step 11: Response to Frontend

The backend returns the structured result payload.

What the frontend receives:
- selected segment timestamps
- ranked candidate matches
- scores
- metadata
- link information

At the end of analysis, the backend returns a response like:
```
{
  "segment": {
    "start": 72.5,
    "end": 78.8
  },
  "matches": [
    {
      "title": "Candidate Song 1",
      "artist": "Artist 1",
      "score": 0.91,
      "spotify_url": "..."
    },
    {
      "title": "Candidate Song 2",
      "artist": "Artist 2",
      "score": 0.82,
      "spotify_url": "..."
    }
  ]
}
```

Why it matters: This completes the backend computation and re-enters the user-facing flow.

### Step 12: Results Display

The frontend displays the candidate match list.

What the system does
- shows match cards
- highlights strongest candidate
- exposes compare and explore actions

Why it matters: This is the point where the user starts making sense of the result. The platform now shifts from analysis to interpretation.

### Step 13: Comparison / Validation

The user plays:
- their selected segment
- one of the candidate results

Technologies involved:
- frontend playback components
- UI comparison player
- optional local crossfade logic

Why it matters: Audio matching is probabilistic. The comparison experience helps the user judge accuracy intuitively. The user validates the system’s guess with their own ears.

### Step 14: Exploration

The user follows outbound links, inspects alternatives, and continues discovery.

Why it matters: This is where the project becomes a discovery platform instead of just a technical tool. The identified match becomes a doorway into broader music exploration.

## User Pipeline from the UI Perspective:

From the UI side, the process is straightforward and interactive:
1. Upload a song
2. View the waveform
3. Listen and scrub through the track
4. Select a suspicious or interesting segment
5. Trigger analysis
6. Wait for candidate matches
7. Inspect ranked results
8. Compare the selected clip against candidates
9. Crossfade or transition between clips
10. Open the likely source elsewhere and continue exploring

From the user’s perspective, the app should feel like:
- a music inspection tool
- a sample-discovery assistant
- a guided validation experience

The UI should always communicate that the platform is helping the user explore likely sample origins, not making an absolute legal claim.

## Data/Music Backend Pipeline

Below is the backend pipeline with technologies and interactions.

1. Request Intake
    - **Technology:** FastAPI
    - Receives file references and timestamps
    - Validates inputs
    - Orchestrates downstream processing

2. File Access and Segment Extraction
    - **Technology:** FFmpeg
    - Takes the uploaded audio file and isolates the selected time window
    - Produces a temporary clip

3. Audio Preprocessing
    - **Technology:** FFmpeg + Python processing utilities
    - Resamples the audio
    - converts to mono
    - normalizes loudness
    - optionally trims silence

4. Fingerprint Generation
    - **Technology:** fingerprint service implemented in Python with chosen audio tools
    - Converts the processed clip into a compact searchable representation

5. Match Retrieval
    - **Technology:** external fingerprint lookup service
    - Searches a known source corpus
    - Returns candidate matches

6. Ranking
    - **Technology:** ranking service
    - Orders results by quality or confidence
    - Filters out low-value matches

7. Metadata Enrichment
    - **Technology:** PostgreSQL + metadata integration utilities
    - Adds display-friendly information to each result

8. Response Assembly
    - **Technology:** FastAPI
    - Packages results into JSON for the frontend

9. Persistence
    - **Technology:** PostgreSQL + object storage
    - Stores uploads, jobs, segments, and results