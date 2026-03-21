# Coding Conventions

**Analysis Date:** 2026-03-21

## Project Status

**Note:** This project is currently in the documentation and specification phase with no implemented source code. The following conventions are recommended based on the specified tech stack (Next.js/TypeScript frontend, FastAPI backend, Python audio processing).

## Naming Patterns

**Files:**
- Frontend components: `PascalCase.tsx` (e.g., `WaveformViewer.tsx`, `SegmentSelector.tsx`)
- API routes: `camelCase.ts` with descriptive names (e.g., `uploadAudio.ts`, `analyzeSegment.ts`)
- Python modules: `snake_case.py` (e.g., `audio_processor.py`, `fingerprint_service.py`)
- Utilities: `camelCase.ts` or `snake_case.py` depending on language

**Functions:**
- Frontend/TypeScript: `camelCase` (e.g., `handleSegmentSelect()`, `extractAudioSegment()`)
- Python: `snake_case` (e.g., `normalize_audio()`, `generate_fingerprint()`)
- Event handlers: `handle{Action}` pattern (e.g., `handleUpload()`, `handlePlayback()`)

**Variables:**
- Frontend: `camelCase` for all variables and constants (e.g., `waveformData`, `selectedSegment`)
- Python: `snake_case` for variables, UPPER_SNAKE_CASE for constants (e.g., `sample_rate`, `FINGERPRINT_LENGTH`)
- React state: descriptive names with `use` prefix for hooks (e.g., `useUploadedFile`, `useSegmentSelection`)

**Types/Interfaces:**
- TypeScript: `PascalCase` (e.g., `AudioFile`, `AnalysisResult`, `MatchCandidate`)
- Python: `PascalCase` for classes (e.g., `AudioProcessor`, `FingerprintService`)
- React component props: `{ComponentName}Props` (e.g., `WaveformViewerProps`)

## Code Style

**Formatting:**
- Tool: Prettier (for TypeScript/JavaScript)
- Tab size: 2 spaces
- Line length: 100 characters
- Semicolons: Required
- Quotes: Double quotes for strings

**Linting:**
- Tool: ESLint (for TypeScript/JavaScript)
- Configuration: TypeScript strict mode enabled
- Rule set: Recommended for Next.js projects
- Additional: rules for React hooks (exhaustive-deps)

## Import Organization

**Order:**
1. External packages and libraries (e.g., `import React from 'react'`, `import { FastAPI } from 'fastapi'`)
2. Internal absolute imports using path aliases (e.g., `import { WaveformViewer } from '@/components/audio'`)
3. Relative imports (e.g., `import { utils } from '../utils'`)
4. Type imports (TypeScript: `import type { ... }`)

**Path Aliases:**
- Frontend recommended: `@/components`, `@/services`, `@/hooks`, `@/utils`, `@/types`
- Keep absolute imports for better readability and refactoring safety

## Error Handling

**Patterns:**
- Frontend: Use try-catch for async operations, display user-friendly error messages
- API Layer: Return structured error responses with appropriate HTTP status codes
- Python: Raise specific exceptions (e.g., `AudioProcessingError`, `InvalidSegmentError`)
- Validation: Validate file types, file sizes, and segment timestamps at API boundaries

**Error Response Format (Recommended):**
```typescript
interface ErrorResponse {
  error: string;
  code: string;
  details?: Record<string, any>;
}
```

## Logging

**Framework:**
- Frontend: `console` (consider upgrading to structured logging like `winston` or `pino` for production)
- Python: `logging` module with appropriate log levels

**Patterns:**
- Log significant operations: file uploads, analysis start/completion, errors
- Include context: file IDs, user actions, timing information
- Avoid logging sensitive audio data or personally identifiable information

## Comments

**When to Comment:**
- Complex audio processing algorithms
- Non-obvious business logic (e.g., fingerprint matching thresholds)
- Workarounds or temporary solutions
- Integration points with external services (AcoustID, music catalog APIs)

**JSDoc/TSDoc:**
- Required for public component and function APIs
- Include parameter descriptions and return types
- Include examples for complex utilities

**Example Pattern:**
```typescript
/**
 * Extracts and analyzes an audio segment from the uploaded file.
 * @param audioBuffer - Raw audio data
 * @param startTime - Segment start in seconds
 * @param endTime - Segment end in seconds
 * @returns Promise<AudioSegment> The processed segment
 */
async function extractSegment(audioBuffer: Buffer, startTime: number, endTime: number): Promise<AudioSegment>
```

## Function Design

**Size:** Keep functions focused and under 30 lines; use helper functions for complex logic

**Parameters:**
- Maximum 3-4 parameters; use objects for multiple related parameters
- Use destructuring for clarity

**Return Values:**
- Functions should return single responsibility results
- Use null/undefined for absence of data (avoid empty objects)
- Use Promise for async operations
- Type all return values explicitly in TypeScript

## Module Design

**Exports:**
- Use named exports for utilities and services (allows better tree-shaking)
- Use default exports for React components
- Group related functions in service modules

**Barrel Files:**
- Use index.ts files to export from directories: `export { Component } from './Component'`
- Maintain clear public API surfaces
- Example: `src/components/audio/index.ts` exports all audio-related components

## API Layer Conventions

**Endpoint Naming:**
- Use RESTful patterns: `/upload`, `/analyze-segment`, `/matches`
- Use HTTP verbs correctly: POST for creation, GET for retrieval, PUT for updates

**Request/Response:**
- All requests/responses should be JSON
- Validate input at route handlers before processing
- Return consistent response envelopes

## Audio Processing Conventions (Python)

**Module Organization:**
- Separate concerns: preprocessing, fingerprinting, matching
- Use classes for stateful services (e.g., `AudioProcessor`, `FingerprintGenerator`)
- Keep pure functions for utility operations

**Dependencies:**
- Primary audio library: FFmpeg for extraction/conversion (via subprocess)
- Alternative: Librosa for analysis-oriented operations
- Consider PyDub for simpler clip manipulation

---

*Convention analysis: 2026-03-21*

**Note:** These conventions are prescriptive for implementation. As actual source code is written, these guidelines should be applied consistently. Establish linting and formatting configurations early in development.
