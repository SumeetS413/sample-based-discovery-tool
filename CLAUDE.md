<!-- GSD:project-start source:PROJECT.md -->
## Project

**Sample Discovery Platform**

A web-based music sample discovery tool that lets users upload a song, visually select a segment they suspect contains a sample, and find the likely original source material through audio fingerprint matching. Users can then compare their segment against candidate matches using an A/B player. Built as a portfolio project that demonstrates full-stack engineering, audio processing, and product design thinking.

**Core Value:** A visitor can upload a song, select a segment, and hear back what it was likely sampled from — the end-to-end pipeline working seamlessly is what makes this impressive.

### Constraints

- **Deployment**: Must deploy easily on Vercel (frontend) + Railway/fly.io (backend) — no complex infra
- **Cost**: Minimal hosting costs — free tiers where possible
- **Dependencies**: FFmpeg must be available in the backend deployment environment
- **External API**: AcoustID availability and rate limits affect matching reliability — hence the curated fallback
- **Audio size**: Reasonable file size limits to keep processing fast and hosting cheap
<!-- GSD:project-end -->

<!-- GSD:stack-start source:codebase/STACK.md -->
## Technology Stack

## Languages
- TypeScript - Client UI layer (Next.js frontend)
- Python - Audio processing and backend API layers
- SQL - Database queries and migrations
## Runtime
- Node.js (for Next.js frontend)
- Python 3.x (for FastAPI backend and audio processing)
- npm/yarn - JavaScript/Node.js dependencies
- pip - Python dependencies
- Lockfiles: Both package-lock.json (Node) and requirements.txt or poetry.lock (Python) expected
## Frameworks
- Next.js - Client UI and lightweight frontend route handlers
- FastAPI - Backend API for audio analysis orchestration and results serving
- React - Core UI framework (via Next.js)
- Tailwind CSS - Styling and component theming
- WaveSurfer.js - Waveform rendering, visualization, and segment region selection
- Not yet specified in architecture documentation
- FFmpeg - Audio extraction, format conversion, resampling, and preprocessing
- TypeScript compiler - Type checking for frontend code
## Key Dependencies
- WaveSurfer.js - Handles waveform visualization and user segment selection; core to user interaction
- FFmpeg - Audio file processing, segment extraction, format conversion, and normalization; essential to pipeline
- FastAPI - Orchestrates audio processing pipeline and serves results; core backend service
- Audio processing libraries (Python) - TBD in implementation (candidates: Librosa for analysis-oriented work, PyDub for simpler manipulation)
- PostgreSQL driver (async/sync TBD) - Database connectivity for metadata and job persistence
- S3-compatible storage client - File upload and audio storage
- Fingerprint service client - Integration with external acoustic fingerprint reference source (AcoustID or similar)
- Next.js API routes - Light frontend-to-backend orchestration
- fetch/axios - HTTP client for API calls to FastAPI backend
## Configuration
- Environment variables used for:
- Next.js configuration (`next.config.js`) - Frontend build and runtime settings
- TypeScript configuration (`tsconfig.json`) - Type checking rules for frontend
- FastAPI configuration (Python) - CORS, middleware, logging settings
## Platform Requirements
- Node.js 16+ - Frontend development
- Python 3.8+ - Backend development
- FFmpeg binary - Must be available in PATH for audio processing
- PostgreSQL database access - For local development
- S3-compatible storage access - For local development (MinIO or AWS)
- Deployment target: Cloud platform supporting Python and Node.js services
- Containerization: Docker expected for both frontend (Next.js) and backend (FastAPI) services
- Database: PostgreSQL (managed cloud instance or self-hosted)
- Object storage: S3-compatible service (AWS S3, DigitalOcean Spaces, etc.)
- Audio processing: FFmpeg must be available in production environment
<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->
## Conventions

## Project Status
## Naming Patterns
- Frontend components: `PascalCase.tsx` (e.g., `WaveformViewer.tsx`, `SegmentSelector.tsx`)
- API routes: `camelCase.ts` with descriptive names (e.g., `uploadAudio.ts`, `analyzeSegment.ts`)
- Python modules: `snake_case.py` (e.g., `audio_processor.py`, `fingerprint_service.py`)
- Utilities: `camelCase.ts` or `snake_case.py` depending on language
- Frontend/TypeScript: `camelCase` (e.g., `handleSegmentSelect()`, `extractAudioSegment()`)
- Python: `snake_case` (e.g., `normalize_audio()`, `generate_fingerprint()`)
- Event handlers: `handle{Action}` pattern (e.g., `handleUpload()`, `handlePlayback()`)
- Frontend: `camelCase` for all variables and constants (e.g., `waveformData`, `selectedSegment`)
- Python: `snake_case` for variables, UPPER_SNAKE_CASE for constants (e.g., `sample_rate`, `FINGERPRINT_LENGTH`)
- React state: descriptive names with `use` prefix for hooks (e.g., `useUploadedFile`, `useSegmentSelection`)
- TypeScript: `PascalCase` (e.g., `AudioFile`, `AnalysisResult`, `MatchCandidate`)
- Python: `PascalCase` for classes (e.g., `AudioProcessor`, `FingerprintService`)
- React component props: `{ComponentName}Props` (e.g., `WaveformViewerProps`)
## Code Style
- Tool: Prettier (for TypeScript/JavaScript)
- Tab size: 2 spaces
- Line length: 100 characters
- Semicolons: Required
- Quotes: Double quotes for strings
- Tool: ESLint (for TypeScript/JavaScript)
- Configuration: TypeScript strict mode enabled
- Rule set: Recommended for Next.js projects
- Additional: rules for React hooks (exhaustive-deps)
## Import Organization
- Frontend recommended: `@/components`, `@/services`, `@/hooks`, `@/utils`, `@/types`
- Keep absolute imports for better readability and refactoring safety
## Error Handling
- Frontend: Use try-catch for async operations, display user-friendly error messages
- API Layer: Return structured error responses with appropriate HTTP status codes
- Python: Raise specific exceptions (e.g., `AudioProcessingError`, `InvalidSegmentError`)
- Validation: Validate file types, file sizes, and segment timestamps at API boundaries
## Logging
- Frontend: `console` (consider upgrading to structured logging like `winston` or `pino` for production)
- Python: `logging` module with appropriate log levels
- Log significant operations: file uploads, analysis start/completion, errors
- Include context: file IDs, user actions, timing information
- Avoid logging sensitive audio data or personally identifiable information
## Comments
- Complex audio processing algorithms
- Non-obvious business logic (e.g., fingerprint matching thresholds)
- Workarounds or temporary solutions
- Integration points with external services (AcoustID, music catalog APIs)
- Required for public component and function APIs
- Include parameter descriptions and return types
- Include examples for complex utilities
## Function Design
- Maximum 3-4 parameters; use objects for multiple related parameters
- Use destructuring for clarity
- Functions should return single responsibility results
- Use null/undefined for absence of data (avoid empty objects)
- Use Promise for async operations
- Type all return values explicitly in TypeScript
## Module Design
- Use named exports for utilities and services (allows better tree-shaking)
- Use default exports for React components
- Group related functions in service modules
- Use index.ts files to export from directories: `export { Component } from './Component'`
- Maintain clear public API surfaces
- Example: `src/components/audio/index.ts` exports all audio-related components
## API Layer Conventions
- Use RESTful patterns: `/upload`, `/analyze-segment`, `/matches`
- Use HTTP verbs correctly: POST for creation, GET for retrieval, PUT for updates
- All requests/responses should be JSON
- Validate input at route handlers before processing
- Return consistent response envelopes
## Audio Processing Conventions (Python)
- Separate concerns: preprocessing, fingerprinting, matching
- Use classes for stateful services (e.g., `AudioProcessor`, `FingerprintGenerator`)
- Keep pure functions for utility operations
- Primary audio library: FFmpeg for extraction/conversion (via subprocess)
- Alternative: Librosa for analysis-oriented operations
- Consider PyDub for simpler clip manipulation
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->
## Architecture

## Pattern Overview
- Five distinct horizontal layers (Client UI, App/API, Audio Processing, Matching, Metadata/Storage)
- Unidirectional data flow from frontend through backend to storage
- Stateless API layer orchestrating work between specialized services
- Audio processing and fingerprinting decoupled from business logic
- External fingerprint lookup service for candidate matching
- Central database coordinating uploads, jobs, segments, and results
## Layers
- Purpose: Interactive user interface for song upload, waveform visualization, segment selection, results exploration, and audio comparison
- Location: Frontend codebase (recommended Next.js with TypeScript and Tailwind CSS)
- Contains: Upload panel, waveform viewer (WaveSurfer.js), segment selector, playback controls, match result cards, comparison player, crossfade interface
- Depends on: App/API Layer for analysis requests and file references
- Used by: End users exploring samples and discovering source material
- Purpose: Stateless orchestration of requests, validation, file handling, and job management between frontend and specialized backend services
- Location: Backend API (recommended FastAPI)
- Contains: Upload endpoints, analyze-segment endpoints, job orchestration, response formatting, timestamp validation, file reference management
- Depends on: Audio Processing Layer, Matching Layer, Metadata/Storage Layer
- Used by: Client UI Layer for all analysis and data requests
- Purpose: Standardization and preparation of audio clips for fingerprinting through format normalization and loudness balancing
- Location: Python-based audio utilities and FFmpeg integration
- Contains: Segment extraction service, audio normalizer, preprocessing utilities, resampling logic, mono conversion, silence trimming
- Depends on: Metadata/Storage Layer for file access
- Used by: App/API Layer during segment analysis workflow
- Purpose: Converting audio clips into searchable representations and querying external fingerprint reference sources to find candidate matches
- Location: Python-based fingerprint service and external API integrations
- Contains: Fingerprint service, match service, ranking service, result filtering, confidence scoring
- Depends on: Audio Processing Layer (receives normalized audio), External fingerprint lookup service (e.g., AcoustID)
- Used by: App/API Layer to retrieve candidate source tracks
- Purpose: Persistent storage of uploaded files, analysis jobs, segment metadata, and enriched match results with external linkage information
- Location: PostgreSQL database and S3-compatible object storage
- Contains: Uploads table, segments table, analysis jobs table, matches table, file storage buckets, metadata associations
- Depends on: Nothing (base layer)
- Used by: All layers above for state persistence and file access
## Data Flow
- **Frontend state:** Current file reference, waveform data, selected segment timestamps, active comparison track, result list
- **Backend state:** Uploaded file records, job status (pending/processing/complete), segment metadata, match results, external link mappings
- **Persistent state:** PostgreSQL stores all permanent records (uploads, segments, jobs, matches), S3-compatible storage holds audio files
- **Transient state:** Temporary extracted clips during processing (cleaned up after job completion)
## Key Abstractions
- Purpose: Represents a user-uploaded audio file with validation, storage, and reference tracking
- Examples: File upload endpoint, uploads table schema, file storage bucket entries
- Pattern: Create unique file_id on upload, store to object storage, record metadata in database for lifecycle tracking
- Purpose: Represents a user-selected time range within an uploaded file with boundaries and analysis request data
- Examples: Segment selection UI component, segments table schema, segment extraction service
- Pattern: Store start_time and end_time as floating-point seconds, validate against file duration, pass to audio processing pipeline
- Purpose: Represents an asynchronous or synchronous analysis request tracking file_id, segment timestamps, and processing status
- Examples: Job orchestration in App/API Layer, analysis jobs table, job status endpoints
- Pattern: Create job record before processing, update status as pipeline progresses, store final results and metadata
- Purpose: Represents normalized audio data prepared for fingerprinting (decoded, resampled, mono, normalized)
- Examples: Preprocessing service outputs, temporary files during processing, fingerprint service inputs
- Pattern: Immutable once created, generated from raw uploaded file via Audio Processing Layer, cleaned up after matching
- Purpose: Represents a compact, machine-comparable acoustic signature of an audio clip suitable for matching
- Examples: Fingerprint service output, external lookup query input, AcoustID integration
- Pattern: Generated from normalized audio, sent to external fingerprint service, used to retrieve candidate matches
- Purpose: Represents a candidate source track returned from fingerprint lookup service with score, metadata, and external links
- Examples: Match objects in ranking service, matches table schema, result display cards
- Pattern: Created from external lookup results, enriched with metadata, ranked by confidence, persisted in database for history
## Entry Points
- Location: Client UI (recommended Next.js application root, `pages/` or `app/` directory depending on Next.js version)
- Triggers: User navigates to application URL, browser loads React/Next.js application
- Responsibilities: Render upload interface, manage waveform visualization state, handle segment selection, trigger analysis requests, display results
## Error Handling
- **File Validation:** Check file format (mp3, wav), file size (max configured), mime type before processing; return 400 Bad Request with descriptive message if invalid
- **Timestamp Validation:** Verify start_time < end_time, both within file duration, minimum segment length (5-10 seconds); return 400 Bad Request if invalid
- **Processing Failures:** Catch audio decoding errors, resampling failures, fingerprint generation errors; log technical details, return generic 500 error to user with "Analysis failed, try again" message
- **External Service Failures:** Handle fingerprint lookup service timeout/unavailable; return empty matches or cached fallback results if applicable; log external service status
- **Storage Failures:** Handle S3 write/read failures, database connection errors; retry with exponential backoff; alert operators if persistent
- **Resource Exhaustion:** Implement file size limits, concurrent job limits to prevent system overload; return 429 Too Many Requests if limits exceeded
## Cross-Cutting Concerns
- Implement structured logging (JSON format) across all layers
- Log entry/exit of major functions with timing information
- Log validation failures with user-safe and technical details
- Log external service calls (fingerprint lookup, enrichment API calls)
- No credential or sensitive data logging
- File validation: format, size, MIME type (at App/API Layer entry)
- Timestamp validation: range, duration, type (at App/API Layer, before Audio Processing)
- Audio validation: successfully decoded, sufficient content, normalization parameters sensible (at Audio Processing Layer)
- Result validation: fingerprint service returns valid response, scoring makes sense, metadata completeness (at Matching Layer)
- MVP: No user authentication required (public analysis platform)
- Future: Optional user accounts for history/bookmarking (JWT tokens, session management at App/API Layer)
- Future: Optional API keys for programmatic access to analyze endpoints
<!-- GSD:architecture-end -->

<!-- GSD:workflow-start source:GSD defaults -->
## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:
- `/gsd:quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd:debug` for investigation and bug fixing
- `/gsd:execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->



<!-- GSD:profile-start -->
## Developer Profile

> Profile not yet configured. Run `/gsd:profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
