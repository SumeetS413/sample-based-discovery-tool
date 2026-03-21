# External Integrations

**Analysis Date:** 2026-03-21

## APIs & External Services

**Audio Fingerprinting:**
- AcoustID (or equivalent acoustic fingerprint service) - Converts audio clips into searchable fingerprints and queries candidate reference corpus
  - SDK/Client: TBD in implementation planning
  - Auth: API key (environment variable)
  - Purpose: Core matching functionality; generates fingerprints and retrieves candidate source tracks

**Music Metadata Enrichment:**
- Music catalog integration (candidates include Spotify, MusicBrainz, or similar) - Enriches match results with title, artist, album, artwork, release metadata
  - SDK/Client: TBD in implementation planning
  - Auth: API key or OAuth token (environment variable)
  - Purpose: Metadata enrichment for user-facing results display

**Music Streaming Services:**
- Spotify (or other DSP) - Provides outbound exploration links and optional in-platform playback
  - SDK/Client: Spotify API client (URL-based links in MVP, deeper integration later)
  - Auth: Client credentials or OAuth (environment variable)
  - Purpose: Links from results for continued user discovery

## Data Storage

**Databases:**
- PostgreSQL - Structured metadata storage
  - Connection: Environment variable (host, port, user, password, database name)
  - Client: Async Python driver (asyncpg or SQLAlchemy async) for FastAPI backend
  - Tables: uploads (file records), segments (selected regions), analysis_jobs (processing state), matches (result tracking), metadata (candidate track information)

**File Storage:**
- S3-compatible object storage (AWS S3, DigitalOcean Spaces, MinIO, etc.) - Persistent storage of uploaded audio files
  - Connection: Environment variables (bucket name, region, endpoint URL)
  - Client: Boto3 (AWS SDK) or s3fs
  - Purpose: Store user-uploaded tracks and analysis-ready clips for retrieval during processing

**Caching:**
- Not explicitly specified in architecture; candidates for future implementation:
  - Redis for temporary job state and fingerprint caching
  - Local filesystem cache for recently processed clips
  - Browser-side caching for frontend assets and waveform data

## Authentication & Identity

**Auth Provider:**
- Custom authentication (later versions may add accounts)
  - Implementation: Session-based or token-based auth for user accounts
  - MVP approach: No user accounts required; stateless analysis requests
  - Future: User registration, login, saved history, bookmarks

## Monitoring & Observability

**Error Tracking:**
- Not specified in architecture documentation
- Candidates for implementation: Sentry, LogRocket, or similar

**Logs:**
- Application logs: Python logging for FastAPI backend, console logs for Next.js frontend
- Job tracking: PostgreSQL records for analysis job status and completion
- Audio processing logs: FFmpeg output and error tracking

## CI/CD & Deployment

**Hosting:**
- Cloud platform supporting containerized Python and Node.js services (AWS ECS, Heroku, Vercel, Railway, etc.)
- Next.js frontend: Deployable to Vercel, AWS Amplify, or containerized on any Node host
- FastAPI backend: Deployable to AWS ECS, Heroku, DigitalOcean App Platform, or self-hosted Kubernetes

**CI Pipeline:**
- Not specified in architecture documentation
- Expected: GitHub Actions or similar for automated testing and deployment
- Build gates: TypeScript type checking, linting (frontend), Python testing and type hints (backend)

## Environment Configuration

**Required env vars:**
- Database: `DATABASE_URL` (PostgreSQL connection string)
- Object storage: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`, `S3_BUCKET_NAME` (or equivalent for chosen storage provider)
- Fingerprint service: `ACOUSTID_API_KEY` (or equivalent service)
- Metadata service: `MUSIC_CATALOG_API_KEY` (or equivalent)
- Music streaming: `SPOTIFY_CLIENT_ID`, `SPOTIFY_CLIENT_SECRET` (for outbound links)
- Backend API: `BACKEND_API_URL` (for frontend to reach FastAPI)
- Frontend: `NEXT_PUBLIC_API_URL` (publicized API endpoint)
- File uploads: `MAX_FILE_SIZE_MB`, `ALLOWED_AUDIO_FORMATS`

**Secrets location:**
- Environment variables stored in secure secret management (AWS Secrets Manager, Vercel Environment Variables, Heroku Config Vars, etc.)
- `.env` files for local development (must include in `.gitignore`)
- CI/CD secrets: Platform-specific secret injection (GitHub Secrets, GitLab CI Variables, etc.)

## Webhooks & Callbacks

**Incoming:**
- Fingerprint service callbacks: Potential async notification of match completion (TBD in implementation)
- No other webhook consumers specified in architecture

**Outgoing:**
- Outbound links only (no webhooks to external services in MVP)
- Spotify deep links for exploration
- Future: Potential webhooks to music catalog services for sync updates

## Data Flow Summary

1. **User uploads audio file** → Frontend uploads via FastAPI endpoint
2. **File stored** → S3-compatible storage + PostgreSQL job record
3. **User selects segment** → Frontend sends timestamps to FastAPI
4. **Backend extracts clip** → FFmpeg processes segment from stored file
5. **Audio preprocessing** → FFmpeg + Python utilities normalize clip
6. **Fingerprint generation** → Fingerprint service (or integrated Python solution) converts clip to searchable representation
7. **Match lookup** → External fingerprint reference source (AcoustID) queried for candidates
8. **Ranking and enrichment** → Results scored and enriched with metadata from music catalog API
9. **Response to frontend** → FastAPI returns ranked matches with metadata
10. **Frontend displays results** → User sees candidates with links to Spotify/DSPs
11. **Comparison/exploration** → User validates with WaveSurfer comparison player or follows outbound links

---

*Integration audit: 2026-03-21*
