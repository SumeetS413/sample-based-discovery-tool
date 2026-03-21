# Directory Structure

**Analysis Date:** 2026-03-21

## Current State

This project is currently in the **design/documentation phase** with no source code implemented yet. The repository contains design documents that define the system architecture, features, and pipeline for a Sample-Based Music Discovery Platform.

## Current Layout

```
Sample-Discovery-Project/
├── OVERVIEW.md                  # Core concept, problem statement, simplified user flow
├── SYSTEM_ARCHITECTURE.md       # Five-layer system design with stack recommendations
├── CORE_FEATURES.md             # 10 core features with explanations + future features
├── PIPELINE_MODEL.md            # Full 14-step pipeline walkthrough with examples
├── AUDIO_PROCESSING.md          # Audio preprocessing/normalization sequence detail
├── USER_FLOW.md                 # 8-step detailed user flow
└── .planning/
    └── codebase/                # GSD codebase mapping output (this analysis)
        ├── STACK.md
        ├── ARCHITECTURE.md
        ├── CONVENTIONS.md
        ├── INTEGRATIONS.md
        ├── STRUCTURE.md
        ├── TESTING.md
        └── CONCERNS.md
```

## Planned Directory Structure

Based on the system architecture documents, the expected implementation layout:

```
Sample-Discovery-Project/
├── frontend/                    # Next.js application (Client UI Layer)
│   ├── src/
│   │   ├── components/          # React components
│   │   │   ├── UploadPanel/     # File upload interface
│   │   │   ├── WaveformViewer/  # WaveSurfer.js waveform display
│   │   │   ├── SegmentSelector/ # Drag-to-select clip region
│   │   │   ├── MatchResults/    # Ranked candidate result cards
│   │   │   ├── ComparisonPlayer/# A/B playback and crossfade
│   │   │   └── ExploreLinks/    # Outbound DSP links
│   │   ├── pages/               # Next.js pages/routes
│   │   └── lib/                 # Shared utilities, API client
│   ├── public/                  # Static assets
│   ├── next.config.js
│   ├── tsconfig.json
│   ├── tailwind.config.js
│   └── package.json
│
├── backend/                     # FastAPI application (App/API + Processing + Matching)
│   ├── app/
│   │   ├── api/                 # API route handlers
│   │   │   ├── upload.py        # Upload endpoints
│   │   │   └── analyze.py       # Analyze-segment endpoints
│   │   ├── services/            # Business logic
│   │   │   ├── audio/           # Audio Processing Layer
│   │   │   │   ├── extractor.py # Segment extraction (FFmpeg)
│   │   │   │   ├── normalizer.py# Preprocessing utilities
│   │   │   │   └── preprocessing.py
│   │   │   ├── matching/        # Matching Layer
│   │   │   │   ├── fingerprint.py # Fingerprint generation
│   │   │   │   ├── matcher.py   # Match retrieval service
│   │   │   │   └── ranker.py    # Result ranking service
│   │   │   └── metadata/        # Metadata enrichment
│   │   ├── models/              # Database models
│   │   │   ├── upload.py        # Uploads table
│   │   │   ├── segment.py       # Segments table
│   │   │   ├── job.py           # Analysis jobs table
│   │   │   └── match.py         # Matches table
│   │   └── config.py            # App configuration
│   ├── requirements.txt
│   └── Dockerfile
│
├── docker-compose.yml           # Local development orchestration
└── .env.example                 # Environment variable template
```

## Key Locations

| Purpose | Expected Path |
|---------|--------------|
| Frontend entry | `frontend/src/pages/index.tsx` |
| API routes | `backend/app/api/` |
| Audio processing | `backend/app/services/audio/` |
| Matching pipeline | `backend/app/services/matching/` |
| Database models | `backend/app/models/` |
| Waveform component | `frontend/src/components/WaveformViewer/` |
| Upload component | `frontend/src/components/UploadPanel/` |

## Naming Conventions (from design docs)

- **Frontend:** PascalCase for components (`WaveformViewer`), camelCase for utilities
- **Backend:** snake_case for Python modules (`fingerprint.py`), classes in PascalCase
- **API endpoints:** RESTful naming (`/api/upload`, `/api/analyze-segment`)
- **Database tables:** Plural snake_case (`analysis_jobs`, `matches`)

## Design Document Index

| Document | Purpose | Key Decisions |
|----------|---------|---------------|
| `OVERVIEW.md` | Project vision and problem statement | Segment-based analysis, interactive validation |
| `SYSTEM_ARCHITECTURE.md` | Five-layer architecture with stack choices | Next.js + FastAPI + FFmpeg + PostgreSQL + S3 |
| `CORE_FEATURES.md` | 10 core features + future roadmap | MVP features vs future (recommendations, accounts) |
| `PIPELINE_MODEL.md` | End-to-end pipeline with 14 steps | Full data flow example with request/response shapes |
| `AUDIO_PROCESSING.md` | Preprocessing normalization detail | Decode → Resample → Mono → Normalize → Trim |
| `USER_FLOW.md` | 8-step user journey | Upload → Waveform → Select → Analyze → Compare → Explore |

---

*Structure analysis: 2026-03-21*
