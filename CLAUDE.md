# CLAUDE.md — Content Catalyst AI

## Project Overview

**Content Catalyst AI** ("LS") is an AI-powered LinkedIn content creation assistant. Users upload documents or describe topics; the app guides them through strategic positioning, angle selection, and draft generation, producing polished LinkedIn posts with SEO analysis, hashtag strategy, and diagnostic refinement.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + TypeScript 5.8 + Vite 5.4 |
| Styling | Tailwind CSS 3.4 + shadcn/ui (Radix UI) |
| Routing | React Router 6 |
| Async state | TanStack React Query 5 |
| Dark mode | next-themes |
| Forms | React Hook Form + Zod |
| Backend | Supabase (PostgreSQL + Deno Edge Functions) |
| PDF parsing | pdf.js 4 + Mammoth (DOCX) + JSZip |
| Testing | Vitest 3 + Testing Library + jsdom |
| Linting | ESLint 9 + typescript-eslint |
| Build | Vite with SWC (fast React plugin) |

---

## Project Structure

```
ContentAnalytics/
├── App.tsx                    # Root app component (providers, routing)
├── App.css                    # Root-level styles
├── index.html                 # HTML entry (references /src/main.tsx)
├── components/                # Source directory — all application code lives here
│   ├── main.tsx               # React entry point (mounted as /src/main.tsx)
│   ├── index.css              # Global CSS + Tailwind base
│   ├── vite-env.d.ts          # Vite environment type declarations
│   │
│   ├── pages/                 # Route-level page components
│   │   ├── Index.tsx          # Main app page (full content creation workflow)
│   │   ├── PDFExtractor.tsx   # Standalone PDF image extractor page
│   │   └── NotFound.tsx       # 404 page
│   │
│   ├── hooks/                 # Custom React hooks
│   │   ├── useSession.ts      # Global session state (messages, drafts, settings)
│   │   ├── useConversationEngine.ts  # AI conversation orchestration & intent detection
│   │   ├── usePDFPipeline.ts  # Large PDF chunking & parallel analysis
│   │   ├── use-mobile.tsx     # Responsive mobile detection
│   │   └── use-toast.ts       # Toast notification hook
│   │
│   ├── services/              # Business logic & Supabase function calls
│   │   ├── aiAnalysis.ts      # Document analysis (calls analyze-document edge fn)
│   │   ├── multimodalAnalysis.ts   # PDF + image multimodal analysis
│   │   ├── aiRefinement.ts    # Draft diagnostics & refinement (calls refine-content)
│   │   ├── pdfParser.ts       # PDF.js integration — text & image extraction
│   │   ├── seoAnalysis.ts     # SEO scoring (calls seo-analyze)
│   │   ├── contentIntelligence.ts  # Content strategy logic
│   │   ├── narrativeIntelligence.ts # Narrative analysis (calls narrative-intelligence)
│   │   ├── topicMemory.ts     # localStorage topic history & authority clusters
│   │   └── railwayImageExtractor.ts # Image extraction utilities
│   │
│   ├── types/
│   │   └── content.ts         # All TypeScript interfaces & union types
│   │
│   ├── integrations/
│   │   └── supabase/
│   │       ├── client.ts      # Supabase client (reads VITE_SUPABASE_* env vars)
│   │       └── types.ts       # Auto-generated Supabase DB types
│   │
│   ├── lib/
│   │   └── utils.ts           # Tailwind utility (cn helper)
│   │
│   ├── ui/                    # shadcn/ui component library (~30 components)
│   │   ├── button.tsx
│   │   ├── dialog.tsx
│   │   ├── resizable.tsx
│   │   └── ... (accordion, card, form, select, sheet, tabs, etc.)
│   │
│   ├── test/
│   │   ├── setup.ts           # Vitest setup (@testing-library/jest-dom)
│   │   └── example.test.ts    # Example test file
│   │
│   ├── ConversationPanel.tsx  # Chat interface (left panel)
│   ├── DraftPanel.tsx         # Draft editor with refinement tools (right panel)
│   ├── StrategyPanel.tsx      # Strategic analysis editor (right panel, strategy phase)
│   ├── ContentBriefPanel.tsx  # Content brief editor & viewer
│   ├── LinkedInPreview.tsx    # LinkedIn post preview mockup
│   ├── WelcomePage.tsx        # Onboarding flow (format & settings selection)
│   ├── SettingsBar.tsx        # Tone/format quick-settings bar
│   ├── PhaseIndicator.tsx     # Progress indicator (setup → strategy → draft → polish)
│   ├── DiagnosticScoreCard.tsx # Content quality diagnostic scores
│   ├── DocumentVisualsGallery.tsx # PDF page image gallery & selection
│   ├── SeoPanel.tsx           # SEO & LLM discoverability scoring
│   ├── HashtagPanel.tsx       # Hashtag strategy display
│   ├── HeadlinePanel.tsx      # Headline variations
│   ├── ImageGeneratorModal.tsx # AI image generation UI
│   ├── PDFImageExtractor.tsx  # PDF image extraction component
│   ├── AuthorityDashboard.tsx # Topic authority cluster dashboard
│   ├── TopicMemoryPanel.tsx   # Topic history panel
│   ├── CompletionPanel.tsx    # Post-draft completion state
│   ├── SectionCards.tsx       # Section display cards
│   └── NavLink.tsx            # Navigation link component
│
├── supabase/
│   ├── config.toml            # Supabase project config (JWT disabled for all fns)
│   ├── functions/             # Deno edge functions (called via supabase.functions.invoke())
│   │   ├── analyze-document/  # Core AI document analysis
│   │   ├── analyze-multimodal/ # Text + image combined analysis
│   │   ├── chunk-document/    # Semantic chunking for large docs (>15 pages)
│   │   ├── synthesize-chunks/ # Merges parallel chunk analyses
│   │   ├── refine-content/    # Draft refinement & diagnostics
│   │   ├── seo-analyze/       # SEO + LLM discoverability scoring
│   │   ├── suggest-headlines/ # Headline generation variants
│   │   ├── trending-hashtags/ # Long-tail hashtag pyramid
│   │   ├── narrative-intelligence/ # Narrative angle & positioning
│   │   └── generate-image/    # AI image generation
│   └── migrations/
│       └── 20260225012024_*.sql   # saved_drafts table schema
│
├── public/                    # Static assets
│
├── package.json               # Scripts & dependencies
├── vite.config.ts             # Vite config (port 8080, @ alias → ./src)
├── vitest.config.ts           # Vitest config (jsdom, globals)
├── tsconfig.app.json          # App TypeScript config
├── tsconfig.json              # Root TypeScript config
├── tailwind.config.ts         # Tailwind config
├── eslint.config.js           # ESLint config
└── components.json            # shadcn/ui config
```

### Important: `src/` vs `components/` naming

The Vite config and tsconfig both set the `@` path alias to `./src`, and `index.html` references `/src/main.tsx`. However, there is **no `src/` directory** — all source files live in `components/`. The configs expect a `src/` directory.

**To run locally**, create a symlink:
```bash
ln -s components src
```

---

## Development Commands

```bash
npm run dev          # Dev server at http://localhost:8080
npm run build        # Production build (dist/)
npm run build:dev    # Dev-mode build
npm run preview      # Serve production build locally
npm run lint         # ESLint check
npm run test         # Run tests once (vitest run)
npm run test:watch   # Run tests in watch mode (vitest)
```

---

## Local Environment Setup

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Create environment file** — copy to `.env.local` at the project root:
   ```env
   VITE_SUPABASE_URL=https://<your-project-ref>.supabase.co
   VITE_SUPABASE_PUBLISHABLE_KEY=<your-anon-key>
   ```
   Find these in your Supabase dashboard under **Project Settings → API**.

3. **Fix the `src/` path alias** (required for Vite to find the entry point):
   ```bash
   ln -s components src
   ```

4. **Start the dev server**
   ```bash
   npm run dev
   # App is served at http://localhost:8080
   ```

5. **(Optional) Supabase local development** — requires [Supabase CLI](https://supabase.com/docs/guides/cli):
   ```bash
   supabase start           # Starts local Supabase stack
   supabase functions serve # Serves edge functions locally
   ```

---

## Key Concepts & Architecture

### Content Creation Phases

The app follows a strict phase progression managed in `useSession.ts`:

| Phase | UI Phase | Description |
|---|---|---|
| `intake` | strategy | User inputs document or topic |
| `analyzing` | strategy | AI processing the source |
| `positioning` | strategy | Strategic analysis displayed in StrategyPanel |
| `angle` | strategy | User selects narrative angle |
| `format` | strategy | User selects output formats |
| `drafting` | draft | AI generates drafts |
| `refine` | draft / polish | User refines with diagnostic tools |
| — | preview | LinkedIn preview before publish |

### Conversation Engine (`useConversationEngine.ts`)

Central orchestrator for user intent. `isProceedIntent()` detects when users signal "move forward" (via text or chip buttons). All user messages flow through `processUserInput()`, which dispatches to the correct phase handler.

### Session State (`useSession.ts`)

Single source of truth for the session. Holds:
- `messages` — chat history
- `drafts` — `{linkedinLong, linkedinShort, sponsoredAds}`
- `settings` — `{tone, seniority, depth}`
- `sourceAnalysis` — AI-extracted analysis
- `strategicPosition` — audience, objective, voice, etc.
- `contentBrief` — confirmed brief (persists across draft/polish phases)
- `phase` / `selectedAngle` / `selectedFormats`

Session is **not persisted to Supabase** — it lives in React state only. Topic memory is persisted to `localStorage`.

### PDF Pipeline (`usePDFPipeline.ts`)

For documents >15 pages, the pipeline:
1. Calls `chunk-document` edge function to split into semantic chunks
2. Analyzes chunks in parallel via `analyze-document`
3. Merges results via `synthesize-chunks`

For ≤15 pages: single `analyze-document` call.

### Supabase Edge Functions

All AI operations go through Supabase edge functions. Called via:
```ts
const { data, error } = await supabase.functions.invoke("function-name", {
  body: { /* payload */ },
});
```

JWT verification is disabled for all functions (`verify_jwt = false` in `config.toml`).

---

## TypeScript Conventions

All core types are in `components/types/content.ts`. Key types:

```ts
type Phase = "intake" | "analyzing" | "positioning" | "angle" | "format" | "drafting" | "refine"
type UIPhase = "setup" | "strategy" | "draft" | "polish" | "preview"
type ToneOption = "authoritative" | "conversational" | "provocative" | "visionary"
type FormatOption = "linkedinLong" | "linkedinShort" | "sponsoredAds"
type AngleOption = "contrarian" | "educational" | "story-driven"

interface SessionState { ... }       // Full session data
interface SourceAnalysis { ... }     // AI-extracted document analysis
interface ContentBrief { ... }       // Confirmed brief before drafting
interface StrategicPosition { ... }  // Audience, objective, voice, etc.
```

TypeScript strictness is **relaxed** (`strict: false`, `noImplicitAny: false`) — the project favours speed of iteration over strict type safety.

---

## Code Conventions

- **Components**: PascalCase files and function names
- **Hooks**: `use*` prefix (e.g. `useSession`, `usePDFPipeline`)
- **Event handlers**: `handle*` prefix (e.g. `handleSendMessage`, `handlePDFUpload`)
- **Callback props**: `on*` suffix (e.g. `onUpdateDraft`, `onComplete`)
- **Path alias**: Use `@/` for all imports within the source (e.g. `@/components/DraftPanel`, `@/hooks/useSession`, `@/types/content`)
- **UI components**: Import from `@/components/ui/*` (shadcn/ui)
- **Supabase client**: Import from `@/integrations/supabase/client`
- **cn()**: Always use the `cn()` utility from `@/lib/utils` for conditional Tailwind classes

---

## Database Schema

Single table in Supabase PostgreSQL:

```sql
CREATE TABLE public.saved_drafts (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title      TEXT NOT NULL DEFAULT 'Untitled Draft',
  format     TEXT NOT NULL DEFAULT 'linkedinLong',
  content    TEXT NOT NULL DEFAULT '',
  image_url  TEXT,
  settings   JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
-- Row Level Security: public access (no auth)
```

---

## Testing

Tests live in `components/test/` and any `*.test.ts(x)` file under `components/`.

```bash
npm run test         # Single run
npm run test:watch   # Watch mode
```

- Test environment: `jsdom` (browser simulation)
- Globals enabled — no need to import `describe`, `it`, `expect`
- Setup: `@testing-library/jest-dom` matchers are auto-imported

---

## UI Layout

The main page uses a **resizable split-panel layout**:

```
┌─────────────────────────────────────────────────────┐
│ Header: Logo + Theme toggle + Visuals button + New  │
├─────────────────────────────────────────────────────┤
│ SettingsBar: Tone / Seniority / Depth / Formats     │
├──────────────────────┬──────────────────────────────┤
│                      │                              │
│  ConversationPanel   │  Right panel (context-aware) │
│  (45% default)       │  (55% default)               │
│                      │  • StrategyPanel             │
│  Chat messages       │  • ContentBriefPanel         │
│  + file upload       │  • DraftPanel                │
│  + quick actions     │  • LinkedInPreview           │
│                      │                              │
└──────────────────────┴──────────────────────────────┘
```

Right panel content switches based on `computedUIPhase` and session state.

---

## Routes

| Path | Component | Description |
|---|---|---|
| `/` | `Index` | Main content creation workflow |
| `/extract` | `PDFExtractorPage` | Standalone PDF image extractor |
| `*` | `NotFound` | 404 page |

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `VITE_SUPABASE_URL` | Yes | Supabase project URL |
| `VITE_SUPABASE_PUBLISHABLE_KEY` | Yes | Supabase anon/public key |

Prefix all frontend env vars with `VITE_` — Vite will expose them to the browser bundle.

---

## Known Structural Issues

1. **No `src/` directory**: Vite config and tsconfig reference `./src`, but source lives in `./components`. Requires `ln -s components src` symlink for local development.
2. **`App.tsx` at root**: The root `App.tsx` uses relative imports (`./pages/Index`) which resolve correctly only when `src/` symlink is in place.
3. **Relaxed TypeScript**: `strict: false` — avoid introducing `any` types unnecessarily even though they won't cause build errors.
4. **No auth**: Supabase Row Level Security is configured with public access. Do not add auth-gated features without updating RLS policies.
