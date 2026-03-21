# Testing Strategy

**Analysis Date:** 2026-03-21

## Current State

No testing framework or test files exist yet. The project is in the design/documentation phase with no source code. This document outlines the testing strategy derived from the architecture and pipeline design documents.

## Recommended Testing Stack

### Frontend (Next.js / TypeScript)

- **Unit/Component Testing:** Jest + React Testing Library
- **E2E Testing:** Playwright or Cypress
- **Visual:** Storybook for component isolation (optional)

### Backend (FastAPI / Python)

- **Unit Testing:** pytest
- **API Testing:** pytest + httpx (FastAPI TestClient)
- **Audio Processing:** pytest with fixture audio files
- **Integration:** pytest with test database (PostgreSQL)

## Test Structure (Planned)

```
frontend/
├── __tests__/
│   ├── components/
│   │   ├── UploadPanel.test.tsx
│   │   ├── WaveformViewer.test.tsx
│   │   ├── SegmentSelector.test.tsx
│   │   ├── MatchResults.test.tsx
│   │   └── ComparisonPlayer.test.tsx
│   └── lib/
│       └── api-client.test.ts
└── e2e/
    └── upload-analyze-flow.spec.ts

backend/
├── tests/
│   ├── api/
│   │   ├── test_upload.py
│   │   └── test_analyze.py
│   ├── services/
│   │   ├── audio/
│   │   │   ├── test_extractor.py
│   │   │   ├── test_normalizer.py
│   │   │   └── test_preprocessing.py
│   │   └── matching/
│   │       ├── test_fingerprint.py
│   │       ├── test_matcher.py
│   │       └── test_ranker.py
│   ├── models/
│   │   └── test_models.py
│   ├── fixtures/
│   │   ├── sample_short.mp3        # 6-second test clip
│   │   ├── sample_stereo.wav       # Stereo test file
│   │   └── sample_silent.mp3       # Edge case: silence
│   └── conftest.py                 # Shared fixtures, test DB setup
```

## Critical Test Areas

### 1. Audio Processing Pipeline
The preprocessing chain (decode → resample → mono → normalize → trim) is the most technically sensitive area. Each step must be independently testable.

**Key tests:**
- Resampling produces correct sample rate
- Stereo-to-mono conversion preserves signal
- Amplitude normalization scales correctly
- Silence trimming removes dead space without cutting signal
- Full pipeline produces consistent output for identical input

### 2. Segment Extraction
FFmpeg-based clip extraction from timestamps is critical to pipeline correctness.

**Key tests:**
- Correct segment extracted given start/end timestamps
- Edge cases: segment at start of track, end of track, very short (<1s), boundary precision
- Invalid timestamps handled (negative, beyond file length, start > end)

### 3. API Endpoints
Upload and analyze endpoints are the system boundary.

**Key tests:**
- File upload accepts valid formats (.mp3, .wav), rejects invalid
- File size limits enforced
- Analyze endpoint validates timestamps
- Response format matches expected JSON schema (segment, matches array with scores)
- Error responses are structured and informative

### 4. Matching and Ranking
Results quality depends on correct ranking logic.

**Key tests:**
- Ranking sorts by score descending
- Low-confidence matches filtered correctly
- Empty results handled gracefully
- Metadata enrichment attaches correct fields

### 5. Frontend Interactions
User-facing components must handle the interactive workflow.

**Key tests:**
- Upload component accepts drag-and-drop and file picker
- Waveform renders from audio data
- Segment selection captures correct timestamps
- Results cards display all metadata fields
- Comparison player supports A/B toggle

## Mocking Strategy

| Dependency | Mock Approach |
|-----------|---------------|
| FFmpeg | Use real FFmpeg with fixture files in CI; mock for unit tests |
| External fingerprint API (AcoustID) | Mock HTTP responses with recorded fixtures |
| PostgreSQL | Test database with migrations; fixture data |
| S3 storage | LocalStack or moto for integration; mock client for unit |
| WaveSurfer.js | Mock canvas/audio APIs in JSDOM |

## Coverage Goals

- **Backend services:** 80%+ line coverage (audio processing, matching, ranking)
- **API endpoints:** 90%+ (all paths including error cases)
- **Frontend components:** 70%+ (interaction logic, not styling)
- **E2E:** Cover the critical path: upload → select segment → analyze → view results

## CI Integration

- Run backend tests with `pytest --cov`
- Run frontend tests with `jest --coverage`
- E2E tests in CI with headless browser
- Audio fixture files committed to repo (small, purpose-built clips)
- FFmpeg must be available in CI environment

---

*Testing analysis: 2026-03-21*
