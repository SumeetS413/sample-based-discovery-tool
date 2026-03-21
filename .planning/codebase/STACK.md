# Technology Stack

**Analysis Date:** 2026-03-21

## Languages

**Primary:**
- TypeScript - Client UI layer (Next.js frontend)
- Python - Audio processing and backend API layers

**Secondary:**
- SQL - Database queries and migrations

## Runtime

**Environment:**
- Node.js (for Next.js frontend)
- Python 3.x (for FastAPI backend and audio processing)

**Package Manager:**
- npm/yarn - JavaScript/Node.js dependencies
- pip - Python dependencies
- Lockfiles: Both package-lock.json (Node) and requirements.txt or poetry.lock (Python) expected

## Frameworks

**Core:**
- Next.js - Client UI and lightweight frontend route handlers
- FastAPI - Backend API for audio analysis orchestration and results serving

**Frontend UI:**
- React - Core UI framework (via Next.js)
- Tailwind CSS - Styling and component theming
- WaveSurfer.js - Waveform rendering, visualization, and segment region selection

**Testing:**
- Not yet specified in architecture documentation

**Build/Dev:**
- FFmpeg - Audio extraction, format conversion, resampling, and preprocessing
- TypeScript compiler - Type checking for frontend code

## Key Dependencies

**Critical:**
- WaveSurfer.js - Handles waveform visualization and user segment selection; core to user interaction
- FFmpeg - Audio file processing, segment extraction, format conversion, and normalization; essential to pipeline
- FastAPI - Orchestrates audio processing pipeline and serves results; core backend service
- Audio processing libraries (Python) - TBD in implementation (candidates: Librosa for analysis-oriented work, PyDub for simpler manipulation)

**Infrastructure:**
- PostgreSQL driver (async/sync TBD) - Database connectivity for metadata and job persistence
- S3-compatible storage client - File upload and audio storage
- Fingerprint service client - Integration with external acoustic fingerprint reference source (AcoustID or similar)

**Frontend Infrastructure:**
- Next.js API routes - Light frontend-to-backend orchestration
- fetch/axios - HTTP client for API calls to FastAPI backend

## Configuration

**Environment:**
- Environment variables used for:
  - Database connection strings (PostgreSQL)
  - S3/object storage credentials and endpoints
  - External fingerprint service API keys and endpoints
  - Backend API base URLs
  - File size and format restrictions

**Build:**
- Next.js configuration (`next.config.js`) - Frontend build and runtime settings
- TypeScript configuration (`tsconfig.json`) - Type checking rules for frontend
- FastAPI configuration (Python) - CORS, middleware, logging settings

## Platform Requirements

**Development:**
- Node.js 16+ - Frontend development
- Python 3.8+ - Backend development
- FFmpeg binary - Must be available in PATH for audio processing
- PostgreSQL database access - For local development
- S3-compatible storage access - For local development (MinIO or AWS)

**Production:**
- Deployment target: Cloud platform supporting Python and Node.js services
- Containerization: Docker expected for both frontend (Next.js) and backend (FastAPI) services
- Database: PostgreSQL (managed cloud instance or self-hosted)
- Object storage: S3-compatible service (AWS S3, DigitalOcean Spaces, etc.)
- Audio processing: FFmpeg must be available in production environment

---

*Stack analysis: 2026-03-21*
