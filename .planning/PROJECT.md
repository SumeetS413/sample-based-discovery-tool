# Sample Discovery Platform

## What This Is

A web-based music sample discovery tool that lets users upload a song, visually select a segment they suspect contains a sample, and find the likely original source material through audio fingerprint matching. Users can then compare their segment against candidate matches using an A/B player. Built as a portfolio project that demonstrates full-stack engineering, audio processing, and product design thinking.

## Core Value

A visitor can upload a song, select a segment, and hear back what it was likely sampled from — the end-to-end pipeline working seamlessly is what makes this impressive.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] User can upload an audio file (.mp3, .wav) through a drag-and-drop or file picker interface
- [ ] Uploaded audio renders as an interactive waveform using WaveSurfer.js
- [ ] User can drag to select a 5-10 second segment on the waveform
- [ ] User can preview playback of their selected segment before analyzing
- [ ] System extracts, preprocesses, and normalizes the selected audio segment
- [ ] System generates an acoustic fingerprint and queries AcoustID for matches
- [ ] System falls back to curated sample pairs when real matching returns weak results
- [ ] Ranked match results display with title, artist, score, and artwork
- [ ] Match confidence shown as visual indicators (bars/labels), not just raw scores
- [ ] User can compare their segment against a match using an A/B playback toggle
- [ ] Outbound links to Spotify or other platforms for matched tracks
- [ ] Pre-loaded demo mode so visitors can try the full flow without uploading a file
- [ ] Step-by-step progress animation during analysis (extracting, fingerprinting, matching...)
- [ ] "How it works" pipeline explainer section showing system design visually
- [ ] Human-friendly error states with helpful guidance (e.g., "Try a longer segment")
- [ ] Responsive design — usable on mobile browsers
- [ ] Frontend deployed on Vercel, backend deployed on Railway or fly.io

### Out of Scope

- Crossfade/blend validation preview — adds complexity without enough portfolio impact for v1
- Full release details in metadata (album, release date) — title/artist/score/artwork is sufficient
- User accounts, saved history, bookmarks — not needed for a portfolio demo
- Automatic segment discovery — manual selection is the core interaction
- Recommendation engine — beyond v1 scope
- Sample family graphs — future feature
- S3 object storage — temp filesystem storage is sufficient for portfolio scale
- PostgreSQL — SQLite is sufficient for v1, clear upgrade path exists
- Support for .m4a or other formats beyond .mp3/.wav — keep validation simple

## Context

- This is a portfolio/resume project driven by personal interest in music sampling culture
- Target audience: recruiters, hiring managers, and engineers evaluating technical ability
- The app must actually work end-to-end when deployed — not a mockup
- Design documents already exist covering architecture, pipeline, features, audio processing, and user flow
- Matching layer uses a hybrid approach: real AcoustID fingerprinting for genuine matching, with curated known sample pairs as fallback to ensure the demo always impresses
- Storage is intentionally simplified for v1 (temp files + SQLite) with a clear upgrade path to Postgres + S3

## Constraints

- **Deployment**: Must deploy easily on Vercel (frontend) + Railway/fly.io (backend) — no complex infra
- **Cost**: Minimal hosting costs — free tiers where possible
- **Dependencies**: FFmpeg must be available in the backend deployment environment
- **External API**: AcoustID availability and rate limits affect matching reliability — hence the curated fallback
- **Audio size**: Reasonable file size limits to keep processing fast and hosting cheap

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Hybrid matching (AcoustID + curated pairs) | Real matching shows technical depth; curated pairs ensure demo reliability | — Pending |
| Simplified storage (filesystem + SQLite) | Reduces infrastructure complexity for portfolio project; clear upgrade path | — Pending |
| Cut crossfade blend from v1 | A/B comparison is sufficient; crossfade adds complexity without enough impact | — Pending |
| Add demo mode with pre-loaded samples | Most visitors won't have audio files ready; instant experience improves portfolio impact | — Pending |
| Add pipeline explainer section | Shows system design thinking; appeals to technical reviewers | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd:transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-03-21 after initialization*
