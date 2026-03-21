# Technical Concerns

**Analysis Date:** 2026-03-21

## Tech Debt

### Unspecified Implementation Details
The project is currently design-only with several critical implementation decisions deferred:

- **Fingerprint service:** No specific library or approach chosen. `PIPELINE_MODEL.md` references "fingerprint service (specifics determined later in planning)" and "audio-processing utilities (specifics determined later in planning)." This is the core technical differentiator and remains undefined.
- **Python audio libraries:** `SYSTEM_ARCHITECTURE.md` lists Librosa and PyDub as candidates but no decision made. These have very different capabilities and performance profiles.
- **Candidate corpus:** The matching layer depends on "a candidate reference corpus" but the source, size, and access pattern are unspecified. AcoustID is mentioned as one option but not committed.
- **Async vs sync backend:** No decision on whether FastAPI endpoints use async handlers, which affects database driver choice and concurrency model.

### No Code Yet
All 6 documents describe the system but no implementation exists. Risk of design-reality divergence increases the longer implementation is deferred.

## Security Concerns

### File Upload Vulnerabilities
- Audio file upload accepts user-provided files with no documented validation beyond format and size
- No mention of malware scanning, file content verification, or upload rate limiting
- Uploaded files stored in S3 — access control policy not specified
- Temporary file handling during processing could leak data if not cleaned up

### Credential Exposure
- Environment variables hold database credentials, S3 keys, and external API keys
- No `.env.example` or secrets management approach documented
- No mention of credential rotation or vault integration

### External API Trust
- Fingerprint lookup sends audio-derived data to external services (AcoustID or similar)
- Privacy implications of sending user audio fingerprints to third parties not addressed
- No fallback if external service is compromised or returns malicious data

## Performance Concerns

### Synchronous Pipeline Risk
- The 14-step pipeline (`PIPELINE_MODEL.md`) appears synchronous: upload → extract → preprocess → fingerprint → match → rank → enrich → respond
- No mention of async job processing, queues, or background workers
- FFmpeg extraction + preprocessing + external API lookup could easily exceed typical HTTP timeout (30s)
- No websocket or polling mechanism documented for long-running analysis

### Audio Processing Bottlenecks
- FFmpeg segment extraction is CPU-intensive for large files
- No mention of file size caps beyond "file validation"
- Preprocessing chain (resample → mono → normalize → trim) runs sequentially per `AUDIO_PROCESSING.md`
- No caching strategy for repeated analysis of the same segment

### External API Latency
- Match lookup depends on external fingerprint service response time
- No timeout, retry, or circuit breaker strategy documented
- Single point of failure for the core feature

## Fragile Areas

### Segment Timestamp Precision
- User selects segments via waveform UI interaction (drag regions)
- Floating-point timestamp precision (e.g., `72.5s` to `78.8s`) must align between:
  - Frontend WaveSurfer.js region selection
  - Backend FFmpeg extraction (`-ss` and `-t` flags)
  - Database storage
- Rounding or precision mismatches could produce subtly wrong clips

### Waveform Rendering Memory
- WaveSurfer.js renders full waveform client-side
- Large audio files (10+ minutes) could cause browser memory issues
- No documented strategy for streaming or chunked rendering

### Ranking Quality
- Ranking service is described abstractly ("scores results," "orders by quality")
- No concrete scoring algorithm, weighting strategy, or threshold defined
- User trust depends heavily on result quality — poor ranking undermines the product

## Scaling Concerns

### Concurrent Users
- No load estimation or concurrency model documented
- Each analysis request triggers: file read + FFmpeg + preprocessing + external API call + DB writes
- Without async processing / job queue, backend could be blocked by concurrent requests

### Storage Growth
- Every uploaded file stored in S3 with no documented retention or cleanup policy
- Analysis jobs, segments, and matches accumulate in PostgreSQL
- No archival or TTL strategy mentioned

## Dependencies at Risk

| Dependency | Risk | Impact |
|-----------|------|--------|
| AcoustID / external fingerprint API | Service availability, rate limits, deprecation | Core matching feature completely blocked |
| FFmpeg | Binary must be available in all environments | Audio processing pipeline fails entirely |
| WaveSurfer.js | Browser compatibility, large file handling | User cannot select segments |
| Unspecified Python audio libs | Decision not made; wrong choice could require rewrite | Preprocessing pipeline blocked |

## Missing Capabilities

### No Offline Fallback
If the external fingerprint service is unavailable, the entire matching feature stops. No local fallback or degraded mode described.

### No Duplicate Detection
No mechanism to detect if a user uploads the same file twice or analyzes the same segment repeatedly. Could lead to wasted processing and storage.

### No Error Recovery
Pipeline failure modes not documented:
- What happens if FFmpeg extraction fails mid-process?
- What if fingerprint generation succeeds but match lookup times out?
- What if metadata enrichment fails for some but not all results?
- No partial result delivery strategy

### No Monitoring/Observability
No logging, metrics, or alerting strategy documented. For a multi-step pipeline with external dependencies, observability is critical for debugging production issues.

## Test Coverage Gaps

Since no code or tests exist yet, all coverage is at 0%. Priority test areas when implementation begins:
- Audio preprocessing chain (highest technical risk)
- Segment timestamp precision (cross-layer data integrity)
- External API integration (mock + contract tests)
- Upload validation (security boundary)
- Ranking consistency (product quality)

---

*Concerns analysis: 2026-03-21*
