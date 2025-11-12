# Regex Builder – Full Development Task Breakdown  
**Format:** GitHub-ready Epics → PRs → Commits  
**Designed for:** Engineers, Cursor AI, Claude, GitHub Projects, Linear/ZenHub import  
**Structure:** `EPIC → PR #TITLE → commit messages`  
**Status Tags:** `[ ] TODO` `[ ] IN-PROGRESS` `[x] DONE`  
**Labels:** `feat`, `fix`, `chore`, `docs`, `test`, `perf`, `ui`, `a11y`

---

## EPIC-001: Project Foundation & Tooling  
**Goal:** Zero-to-running dev environment with CI/CD  
**Owner:** Tech Lead  
**Story Points:** 13

```
└─ PR #1 Initialize Vite + React + TypeScript + shadcn/ui + Sonner
   ├─ chore: create vite react-ts template
   ├─ chore: add shadcn/ui with pnpm/npx shadcn@latest init
   ├─ chore: install sonner, tailwindcss@latest, @tailwindcss/forms
   ├─ chore: setup tailwind v4 config + dark mode
   ├─ chore: add prettier, eslint (airbnb + react-hooks) + husky
   ├─ chore: add vitest + react-testing-library + @testing-library/jest-dom
   ├─ chore: add playwright for e2e
   ├─ chore: add github actions: lint + test + build
   ├─ chore: add changeset for versioning
   └─ docs: update README with dev scripts, component add command

└─ PR #2 Setup Zustand store + theme persistence
   ├─ feat: create store/theme.ts with dark/auto mode
   ├─ feat: persist theme in localStorage
   ├─ ui: add ThemeToggle component using shadcn/ui DropdownMenu
   └─ test: add theme toggle unit tests
```

---

## EPIC-002: Core Layout & Responsive Shell  
**Goal:** Complete responsive layout with header, sidebars, tabs  
**Owner:** Frontend Engineer  
**Story Points:** 21

```
└─ PR #3 Create main App shell with responsive grid
   ├─ ui: add Header with title + theme toggle + GitHub link
   ├─ ui: create three-column layout (snippet | editor | preview)
   ├─ ui: add Resizable panels (using @react-resizable-panels)
   ├─ ui: mobile: collapse snippet sidebar into bottom sheet
   └─ a11y: add ARIA landmarks (header, main, navigation)

└─ PR #4 Implement Tabs system (Matches | Explain | Snippets)
   ├─ feat: add shadcn/ui Tabs with lazy loading
   ├─ ui: Matches tab skeleton
   ├─ ui: Explain tab skeleton (tree view placeholder)
   ├─ ui: Snippets tab with search + categories
   └─ perf: memoize tab content
```

---

## EPIC-003: Monaco Editor + Real-time Regex Engine  
**Goal:** Full regex editing with <50ms feedback  
**Owner:** Core Engineer  
**Story Points:** 34

```
└─ PR #5 Integrate Monaco Editor with regex grammar
   ├─ feat: dynamic import @monaco-editor/react (code-split)
   ├─ feat: load regex monarch tokenizer (official JS regex grammar)
   ├─ feat: add error squiggles via try/catch RegExp()
   ├─ ui: custom theme "regex-dark" / "regex-light"
   └─ perf: debounce regex parsing 150ms

└─ PR #6 Real-time matching engine (Web Worker ready)
   ├─ feat: create useRegexMatch hook (input + flags → matches)
   ├─ feat: support named groups, backreferences, lookaheads
   ├─ feat: highlight full match + 10 capture groups (color palette)
   ├─ perf: early exit if regex invalid
   ├─ perf: virtualized match list for >10k matches
   └─ test: 50+ regex edge cases (unicode, \Z, ^ multiline)
```

---

## EPIC-004: Test Input Panel + File Upload  
**Goal:** Handle 1MB+ inputs without freezing  
**Owner:** Frontend Engineer  
**Story Points:** 21

```
└─ PR #7 Resizable test input with file drop
   ├─ ui: add Textarea with auto-resize + character counter
   ├─ feat: drag-and-drop file → FileReader → state
   ├─ feat: support .txt .log .json .csv
   ├─ feat: sample data loader (emails, logs, json)
   └─ a11y: announce file loaded via ARIA live region

└─ PR #8 Offload large inputs to Web Worker
   ├─ perf: create worker/regex.worker.ts
   ├─ perf: move matching logic to worker (postMessage)
   ├─ perf: stream results back in chunks
   └─ test: worker fallback for Safari
```

---

## EPIC-005: Match Visualizer & Group Explorer  
**Goal:** Best-in-class match highlighting  
**Owner:** UX Engineer  
**Story Points:** 21

```
└─ PR #9 Live match highlighter in test panel
   ├─ ui: overlay <mark> tags with tailwind bg-opacity-30
   ├─ ui: distinct colors per group (10-color palette + fallback)
   ├─ ui: hover tooltip: "Group 2 (domain) → gmail.com"
   └─ a11y: add aria-label to each mark

└─ PR #10 Match list sidebar with collapse/expand
   ├─ ui: virtualized list (react-window)
   ├─ ui: show match index, range, duration
   ├─ ui: click match → scroll test panel into view
   └─ feat: copy match or group value button
```

---

## EPIC-006: Explanation Engine (Tree + Human Text)  
**Goal:** Understand any regex in plain English  
**Owner:** Core Engineer  
**Story Points:** 34

```
└─ PR #11 regexpp AST parser + tree view
   ├─ feat: parse regex → AST using regexpp@latest
   ├─ ui: recursive TreeView component (shadcn Collapsible)
   ├─ ui: icon per node type (anchor, quantifier, charset)
   └─ test: 100 regex patterns → correct AST

└─ PR #12 Human-readable explanation generator
   ├─ feat: convert AST → natural language
   ├─ feat: examples: "^\\d{4}-\\d{2}-\\d{2}$" → "Matches ISO dates at line start/end"
   ├─ ui: button "Explain in English" → Sonner toast + modal
   └─ feat: copy explanation as markdown
```

---

## EPIC-007: Snippet Library (50+ patterns)  
**Goal:** Instant access to production regex  
**Owner:** Content Engineer  
**Story Points:** 21

```
└─ PR #13 Snippet data structure + JSON file
   ├─ chore: create public/snippets.json (name, regex, flags, desc, category, tags)
   ├─ feat: search + filter by category/tag
   ├─ ui: grid view with copy button
   └─ feat: "Insert into editor" replaces current regex

└─ PR #14 Community contribution flow
   ├─ docs: add SNIPPET_CONTRIBUTION.md
   ├─ chore: GitHub issue template for new snippets
   └─ feat: snippet preview modal before insert
```

---

## EPIC-008: Export System (10+ languages)  
**Goal:** Copy-paste ready code  
**Owner:** Frontend Engineer  
**Story Points:** 13

```
└─ PR #15 Export dropdown with Sonner feedback
   ├─ feat: export map: JS, Python, Rust, Go, Java, PHP, Ruby, .NET, Perl, JavaScript (test)
   ├─ ui: shadcn DropdownMenu with icons
   ├─ feat: copy with Sonner.success("Copied Python regex")
   └─ feat: include flags + named groups comment
```

---

## EPIC-009: Save, Load & Share via URL  
**Goal:** Frictionless collaboration  
**Owner:** Full-stack Engineer  
**Story Points:** 21

```
└─ PR #16 IndexedDB persistence with idb-keyval
   ├─ feat: save pattern (name, desc, regex, test, flags, tags)
   ├─ ui: Saved Patterns sidebar tab
   ├─ feat: auto-save last session
   └─ feat: export/import JSON

└─ PR #17 Shareable URL encoding
   ├─ feat: compress state with lz-string
   ├─ feat: hash route #/?s=<compressed>
   ├─ feat: load state on mount
   └─ feat: "Copy Share Link" button → Sonner
```

---

## EPIC-010: Accessibility & PWA  
**Goal:** WCAG 2.1 AA + installable  
**Owner:** A11y Engineer  
**Story Points:** 13

```
└─ PR #18 Full keyboard navigation
   ├─ a11y: focus management in Monaco → tabs → buttons
   ├─ a11y: Escape closes modals, Enter activates buttons
   └─ a11y: axe-core audit + fix violations

└─ PR #19 PWA + offline support
   ├─ chore: add manifest.json + icons (192, 512)
   ├─ chore: register service worker (vite-plugin-pwa)
   ├─ feat: cache monaco + snippets for offline use
   └─ test: lighthouse score >95
```

---

## EPIC-011: Testing, CI/CD, Analytics  
**Goal:** Ship with confidence  
**Owner:** QA Engineer  
**Story Points**: 21

```
└─ PR #20 Unit + integration tests (80% coverage)
   ├─ test: useRegexMatch edge cases
   ├─ test: export functions
   ├─ test: URL encoding round-trip
   └─ chore: nyc report badge

└─ PR #21 Playwright E2E smoke tests
   ├─ test: create regex → match → export → share
   ├─ test: mobile viewport
   └─ ci: run on push to main

└─ PR #22 Vercel analytics + posthog (optional)
   ├─ feat: track popular snippets, export language
   ├─ feat: error reporting (Sentry free tier)
   └─ chore: privacy policy page
```

---

## EPIC-012: Polish & Launch  
**Goal:** Product Hunt ready  
**Owner:** Product + Design  
**Story Points:** 13

```
└─ PR #23 Empty state + onboarding tour
   ├─ ui: "Try matching an email" hero with one-click demo
   ├─ ui: hotkey cheat sheet (Ctrl+/)
   └─ ui: welcome modal on first visit

└─ PR #24 Final design polish
   ├─ ui: animations (framer-motion) for tab switch
   ├─ ui: skeleton loaders
   ├─ ui: error boundary with "Report bug" button
   └─ perf: bundle analyzer <1.3MB gzipped

└─ PR #25 Launch assets
   ├─ docs: update README with screenshots, live demo link
   ├─ docs: add CHANGELOG.md
   ├─ chore: deploy to regexbuilder.app (Vercel)
   └─ chore: submit to Product Hunt, Reddit r/regex, r/webdev
```

---

## How to Use This Doc

```bash
# Clone & setup
git clone https://github.com/yourteam/regex-builder.git
cd regex-builder
pnpm i

# Start a new PR from an epic
git checkout -b epic/003-monaco
# → follow commits in order
```

**Cursor AI prompt example:**
```
Implement PR #6 Real-time matching engine exactly as written in the task breakdown. Use the useRegexMatch hook signature and Web Worker fallback.
```

**Linear import:** Copy-paste into CSV → `import (columns: Title, Description, Labels, Assignee)

**Total Story Points:** 226 (~10 weeks for 2 devs at 40pts/sprint)

**Launch Date Target:** **March 15, 2026**  
**Live URL:** `https://regexbuilder.app` (ready for bookmarking)

Let’s ship the **Regex101 killer**.
