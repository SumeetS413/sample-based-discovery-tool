# Architecture

**Analysis Date:** 2026-03-21

## Pattern Overview

**Overall:** Layered architecture with clear separation of concerns across five independent layers, following a request-response pipeline for sample discovery and audio analysis.

**Key Characteristics:**
- Five distinct horizontal layers (Client UI, App/API, Audio Processing, Matching, Metadata/Storage)
- Unidirectional data flow from frontend through backend to storage
- Stateless API layer orchestrating work between specialized services
- Audio processing and fingerprinting decoupled from business logic
- External fingerprint lookup service for candidate matching
- Central database coordinating uploads, jobs, segments, and results

## Layers

**Client UI Layer:**
- Purpose: Interactive user interface for song upload, waveform visualization, segment selection, results exploration, and audio comparison
- Location: Frontend codebase (recommended Next.js with TypeScript and Tailwind CSS)
- Contains: Upload panel, waveform viewer (WaveSurfer.js), segment selector, playback controls, match result cards, comparison player, crossfade interface
- Depends on: App/API Layer for analysis requests and file references
- Used by: End users exploring samples and discovering source material

**App/API Layer:**
- Purpose: Stateless orchestration of requests, validation, file handling, and job management between frontend and specialized backend services
- Location: Backend API (recommended FastAPI)
- Contains: Upload endpoints, analyze-segment endpoints, job orchestration, response formatting, timestamp validation, file reference management
- Depends on: Audio Processing Layer, Matching Layer, Metadata/Storage Layer
- Used by: Client UI Layer for all analysis and data requests

**Audio Processing Layer:**
- Purpose: Standardization and preparation of audio clips for fingerprinting through format normalization and loudness balancing
- Location: Python-based audio utilities and FFmpeg integration
- Contains: Segment extraction service, audio normalizer, preprocessing utilities, resampling logic, mono conversion, silence trimming
- Depends on: Metadata/Storage Layer for file access
- Used by: App/API Layer during segment analysis workflow

**Matching Layer:**
- Purpose: Converting audio clips into searchable representations and querying external fingerprint reference sources to find candidate matches
- Location: Python-based fingerprint service and external API integrations
- Contains: Fingerprint service, match service, ranking service, result filtering, confidence scoring
- Depends on: Audio Processing Layer (receives normalized audio), External fingerprint lookup service (e.g., AcoustID)
- Used by: App/API Layer to retrieve candidate source tracks

**Metadata / Storage Layer:**
- Purpose: Persistent storage of uploaded files, analysis jobs, segment metadata, and enriched match results with external linkage information
- Location: PostgreSQL database and S3-compatible object storage
- Contains: Uploads table, segments table, analysis jobs table, matches table, file storage buckets, metadata associations
- Depends on: Nothing (base layer)
- Used by: All layers above for state persistence and file access

## Data Flow

**Upload and Segment Selection Flow:**

1. User uploads audio file through UI
2. App/API Layer receives file, validates format and size, stores in object storage, creates uploads table record with file reference
3. Client UI renders waveform visualization from uploaded file
4. User selects segment time range (start_time, end_time) in UI
5. Frontend sends analysis request with file_id, start_time, end_time to App/API Layer

**Segment Processing and Matching Flow:**

1. App/API Layer validates timestamps against stored file metadata
2. Audio Processing Layer extracts the selected clip using FFmpeg from stored file (temp output)
3. Audio Processing Layer normalizes extracted clip: decode → resample to standard rate (22050 Hz or 44100 Hz) → mono conversion → amplitude normalization → silence trimming
4. Matching Layer receives normalized audio and generates acoustic fingerprint representation
5. Matching Layer queries external fingerprint lookup service (e.g., AcoustID) with fingerprint
6. Matching Layer receives raw candidate matches, scores them, and ranks by confidence/similarity
7. App/API Layer enriches ranked results with metadata (title, artist, album, artwork) from candidate corpus or database
8. App/API Layer formats response payload with segment timestamps, ranked matches, scores, and external links
9. Response returns to Client UI Layer with structured candidate results

**Result Validation and Exploration Flow:**

1. Client UI displays ranked match results with metadata cards
2. User selects match to compare and frontend loads comparison player
3. Comparison player provides A/B playback and optional crossfade preview between selected segment and candidate track
4. User follows outbound listening links to external music platforms (Spotify, etc.)
5. App/API Layer optionally persists comparison actions and user exploration history to database

**State Management:**

- **Frontend state:** Current file reference, waveform data, selected segment timestamps, active comparison track, result list
- **Backend state:** Uploaded file records, job status (pending/processing/complete), segment metadata, match results, external link mappings
- **Persistent state:** PostgreSQL stores all permanent records (uploads, segments, jobs, matches), S3-compatible storage holds audio files
- **Transient state:** Temporary extracted clips during processing (cleaned up after job completion)

## Key Abstractions

**Upload:**
- Purpose: Represents a user-uploaded audio file with validation, storage, and reference tracking
- Examples: File upload endpoint, uploads table schema, file storage bucket entries
- Pattern: Create unique file_id on upload, store to object storage, record metadata in database for lifecycle tracking

**Segment:**
- Purpose: Represents a user-selected time range within an uploaded file with boundaries and analysis request data
- Examples: Segment selection UI component, segments table schema, segment extraction service
- Pattern: Store start_time and end_time as floating-point seconds, validate against file duration, pass to audio processing pipeline

**AnalysisJob:**
- Purpose: Represents an asynchronous or synchronous analysis request tracking file_id, segment timestamps, and processing status
- Examples: Job orchestration in App/API Layer, analysis jobs table, job status endpoints
- Pattern: Create job record before processing, update status as pipeline progresses, store final results and metadata

**AudioClip:**
- Purpose: Represents normalized audio data prepared for fingerprinting (decoded, resampled, mono, normalized)
- Examples: Preprocessing service outputs, temporary files during processing, fingerprint service inputs
- Pattern: Immutable once created, generated from raw uploaded file via Audio Processing Layer, cleaned up after matching

**Fingerprint:**
- Purpose: Represents a compact, machine-comparable acoustic signature of an audio clip suitable for matching
- Examples: Fingerprint service output, external lookup query input, AcoustID integration
- Pattern: Generated from normalized audio, sent to external fingerprint service, used to retrieve candidate matches

**Match:**
- Purpose: Represents a candidate source track returned from fingerprint lookup service with score, metadata, and external links
- Examples: Match objects in ranking service, matches table schema, result display cards
- Pattern: Created from external lookup results, enriched with metadata, ranked by confidence, persisted in database for history

## Entry Points

**User-Facing Entry Point:**
- Location: Client UI (recommended Next.js application root, `pages/` or `app/` directory depending on Next.js version)
- Triggers: User navigates to application URL, browser loads React/Next.js application
- Responsibilities: Render upload interface, manage waveform visualization state, handle segment selection, trigger analysis requests, display results

**API Entry Points:**

1. **Upload Endpoint:**
   - Location: App/API Layer (FastAPI route, e.g., `POST /api/upload`)
   - Triggers: Client UI form submission with audio file
   - Responsibilities: Receive file, validate format/size, store to object storage, create uploads table record, return file_id reference

2. **Analyze Segment Endpoint:**
   - Location: App/API Layer (FastAPI route, e.g., `POST /api/analyze`)
   - Triggers: Client UI form submission with file_id, start_time, end_time
   - Responsibilities: Validate timestamps, orchestrate Audio Processing → Matching → enrichment pipeline, return ranked results

3. **Results Endpoint:**
   - Location: App/API Layer (FastAPI route, e.g., `GET /api/results/{job_id}`)
   - Triggers: Client UI polling or initial response fetch
   - Responsibilities: Retrieve persisted analysis results from database, return formatted candidate matches with metadata

## Error Handling

**Strategy:** Validation-first approach with clear error propagation through layers, user-facing error messages in API responses, logging of technical errors for debugging.

**Patterns:**

- **File Validation:** Check file format (mp3, wav), file size (max configured), mime type before processing; return 400 Bad Request with descriptive message if invalid
- **Timestamp Validation:** Verify start_time < end_time, both within file duration, minimum segment length (5-10 seconds); return 400 Bad Request if invalid
- **Processing Failures:** Catch audio decoding errors, resampling failures, fingerprint generation errors; log technical details, return generic 500 error to user with "Analysis failed, try again" message
- **External Service Failures:** Handle fingerprint lookup service timeout/unavailable; return empty matches or cached fallback results if applicable; log external service status
- **Storage Failures:** Handle S3 write/read failures, database connection errors; retry with exponential backoff; alert operators if persistent
- **Resource Exhaustion:** Implement file size limits, concurrent job limits to prevent system overload; return 429 Too Many Requests if limits exceeded

## Cross-Cutting Concerns

**Logging:**
- Implement structured logging (JSON format) across all layers
- Log entry/exit of major functions with timing information
- Log validation failures with user-safe and technical details
- Log external service calls (fingerprint lookup, enrichment API calls)
- No credential or sensitive data logging

**Validation:**
- File validation: format, size, MIME type (at App/API Layer entry)
- Timestamp validation: range, duration, type (at App/API Layer, before Audio Processing)
- Audio validation: successfully decoded, sufficient content, normalization parameters sensible (at Audio Processing Layer)
- Result validation: fingerprint service returns valid response, scoring makes sense, metadata completeness (at Matching Layer)

**Authentication:**
- MVP: No user authentication required (public analysis platform)
- Future: Optional user accounts for history/bookmarking (JWT tokens, session management at App/API Layer)
- Future: Optional API keys for programmatic access to analyze endpoints

---

*Architecture analysis: 2026-03-21*
