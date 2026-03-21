# Detailed User Flow

## Step 1: Song Input

The user enters the system through an upload interface. For the MVP, they upload an audio file such as .mp3 or .wav. In a later version, this could be extended to include catalog search and track selection via a DSP or music API.

At this stage, the system now has:
- the raw song file
- basic file metadata
- a unique file reference for downstream processing

## Step 2: Waveform Rendering

Once the upload is complete, the platform renders the track waveform in the UI. This gives the user a visual timeline of the audio and makes it possible to inspect and select a specific region.

At this stage, the system is turning the audio from “an uploaded file” into “an interactive object.”

## Step 3: Segment Selection

The user highlights a short region of the waveform, ideally one that sounds familiar or sample-like. For example, they may choose a section from `1:12.5` to `1:18.8`.

At this stage, the system now has:
- the file reference
- start timestamp
- end timestamp

## Step 4: Analysis Request

The user clicks an action such as Analyze Segment. The frontend sends the selected timestamps and file identifier to the backend.

At this point, the system transitions from interaction to processing.

## Step 5: Segment Processing and Matching

The backend extracts the selected clip, preprocesses it, creates a comparable representation such as a fingerprint, and searches a reference source for likely matches.

At this stage, the user does not see what is happening directly, but this is where the technical core of the application runs.

## Step 6: Result Presentation

The system returns ranked candidate matches and displays them in the UI. Each result may include:
- track title
- artist
- score or confidence indicator
- artwork
- listen or compare action
- outbound listening link

## Step 7: Comparison and Validation

The user listens to their selected segment and compares it against one or more candidate results. This may happen through:
- A/B playback
- side-by-side playback
- simple crossfade preview

This stage is essential because it turns abstract matching into an intuitive verification experience.

## Step 8: Exploration

The user can click through to listen to the source track elsewhere, inspect alternate matches, and eventually receive suggestions for adjacent discovery.