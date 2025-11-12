# Product Requirements Document (PRD): Regex Builder

## 1. Overview
### 1.1 Product Description
**Regex Builder** is a modern, web-based visual regex editor that enables developers, testers, data engineers, and analysts to **build, test, debug, and share regular expressions** with zero friction. Inspired by Regex101 but rebuilt from the ground up with performance, extensibility, and developer experience in mind.

Using real-time syntax highlighting, live match visualization, intelligent explanations, and one-click export to multiple languages, Regex Builder transforms regex from a cryptic black art into an intuitive, visual workflow tool.

The app is powered by **Vite + React + TypeScript**, styled with **Tailwind CSS v4**, built on **shadcn/ui** components, and uses **Sonner** for toast notifications — ensuring a fast, accessible, and beautiful experience.

### 1.2 Version
- **MVP Version**: 1.0  
- **Release Date Target**: Q1 2026 (Public beta in 6 weeks)

### 1.3 Target Audience
- **Primary Users**: Frontend/backend developers, QA engineers, data scientists, security analysts  
- **Secondary Users**: Technical writers, educators, students learning regex  
- **Assumptions**: Users know what regex is but struggle with syntax, debugging, or cross-language differences

## 2. Goals and Objectives
### 2.1 Business Goals
- Become the **fastest, most intuitive open-source regex tool** on the web
- Achieve **10,000 monthly active users** within 6 months of launch
- Establish Regex Builder as the **default regex playground** in developer communities (Reddit, Stack Overflow, Dev.to)
- Enable future monetization via cloud sync, AI suggestions, and team collaboration (v2+)

### 2.2 User Goals
- Write correct regex **10x faster** than manual trial-and-error
- Understand **exactly** what a regex does without external docs
- Copy production-ready regex **instantly** into JS, Python, Rust, Go, etc.
- Share complex patterns with teammates via a single link

### 2.3 Success Metrics
- **Quantitative**:
  - Average time to first valid match: **< 800ms**
  - 95% of users successfully match their test string on first try
  - >60% of sessions include exporting or copying a regex
- **Qualitative**:
  - NPS > 9/10 in post-use survey
  - “This replaced Regex101 for me” appears in >30% of feedback

## 3. User Stories
As a [user], I want [feature] so that [benefit].

| User Story ID | Description | Priority |
|---------------|-------------|----------|
| US-001 | As a developer, I want to type a regex and see matches highlighted instantly so I can validate behavior in real time | Must-Have |
| US-002 | As a beginner, I want hover tooltips explaining each token (`\d+`, `(?=...)`, etc.) so I can learn as I build | Must-Have |
| US-003 | As a data engineer, I want to load a 10K-line log file and see all matches grouped so I can extract fields confidently | Must-Have |
| US-004 | As a QA engineer, I want one-click copy in Python/JS/Go/Rust formats so I can paste directly into tests | Must-Have |
| US-005 | As a team lead, I want to generate a shareable link with regex + test data so I can collaborate without screenshots | Should-Have |
| US-006 | As a security analyst, I want pre-built SIGMA/YARA-style snippet library so I can detect threats faster | Should-Have |
| US-007 | As a user, I want saved patterns in localStorage with tags and descriptions so I can reuse them across projects | Could-Have |
| US-008 | As an advanced user, I want regex flavor toggle (JavaScript vs Python vs PCRE) so behavior matches my runtime | Won't-Have (v1) |

## 4. Functional Requirements
### 4.1 Core Features

#### **Regex Editor**
- Monaco Editor (or CodeMirror) with:
  - Full regex syntax highlighting
  - Real-time error detection (red underline + tooltip)
  - Auto-close brackets, smart flag suggestions
- Flag toggles as **beautiful chips** (`g` `i` `m` `s` `u` `y`) with descriptions

#### **Test Input Panel**
- Resizable textarea supporting:
  - Multi-line input
  - File drag-and-drop (TXT, JSON, CSV, logs)
  - Sample dataset loader (e.g., “100 emails”, “Apache logs”)
- Live counter: `Matches: 42 | Groups: 3 | Time: 4ms`

#### **Match Visualizer**
- Color-coded highlights:
  - Full match: **bold blue background**
  - Capture groups: **distinct pastel colors** (group 1 = pink, group 2 = green, etc.)
  - Hover to show group name/index
- Match list sidebar:
  ```text
  Match 1 (0-11) → john@site.com
    └── Group 1 → john
    └── Group 2 → site.com
  ```

#### **Explanation Engine**
- Powered by enhanced `regexpp` + custom parser
- Tree view breakdown:
  ```
  ├─ ^              → Start of line
  ├─ (?<name>\w+)    → Named capture "name" → word characters
  ├─ @               → Literal @
  ├─ (?<domain>...)  → Named capture "domain"
  └─ $               → End of line
  ```
- One-click “Explain in human language” → “Matches emails with named groups for username and domain”

#### **Snippet Library**
- Searchable, categorized sidebar
- Built-in 50+ production-ready patterns:

| Category | Examples |
|--------|----------|
| Validation | Email (RFC-compliant), URL, UUID, JWT, SemVer |
| Extraction | Dates (ISO, US, EU), IPs (v4/v6), Credit Cards (masked), Log levels |
| Security | SQLi payloads, XSS scripts, secrets (API keys, tokens) |
| Data | CSV fields, JSON paths, HTML tags (safe) |

#### **Export System**
- “Copy As” dropdown:
  ```text
  ▾ JavaScript      → /pattern/gim
  ▾ Python          → r"pattern"
  ▾ Python (re)     → re.compile(r"...", re.I|re.M)
  ▾ Rust            → regex::Regex::new(r"...")
  ▾ Go              → regexp.MustCompile(`...`)
  ▾ Test Code (JS)  → Full test function with expect()
  ```

#### ++ **Share & Persistence**
- Generate URL with full state:
  ```
  https://regexbuilder.app/#/?r=^(%3F%3Cname%3E%5Cw%2B)&t=john%40example.com&f=gim
  ```
- Save to IndexedDB with metadata (name, tags, description)

### 4.2 Extensibility Hooks
- Snippet contribution via GitHub (community-driven)
- Plugin API (future): custom parsers, themes, AI suggest

## 5. Non-Functional Requirements
### 5.1 Performance
- Regex parsing & matching: **< 50ms** on 10K-line input
- Initial load: **< 1.2s** (fully bundled + brotli)
- No jank during typing (debounced updates)

### 5.2 Accessibility
- Full keyboard navigation (Tab, arrows, Enter)
- ARIA labels on all interactive elements
- High-contrast mode (Tailwind `dark:` + `contrast-more`)
- Screen reader announces: “Match found at position 10–25”

### 5.3 Security
- All input sanitized (no XSS in test data)
- No `eval()` or `new Function()` in any transform
- Share links rate-limited and expire after 90 days (v2)

### 5.4 Compatibility
- Chrome 100+, Firefox 98+, Safari 16+, Edge 100+
- Fully responsive: mobile touch support (pinch zoom, tap to select)
- PWA installable (manifest + service worker)

## 6. UI/UX Design Guidelines
### **Layout**
```
┌────────────────────────────────────────────────────┐
│ Header: Regex Builder 🧩  [Theme] [Feedback] [GitHub] │
├──────────────┬─────────────────────────────────────┤
│ Snippet      │  Regex Editor + Flags                  │
│ Library      │  ┌─────────────────────────────────┐  │
│ (Searchable) │  │ Monaco Editor (regex)           │  │
│              │  └─────────────────────────────────┘  │
│              │  Test Input (resizable)               │
│              │  ┌─────────────────────────────────┐  │
│              │  │ Live Match Highlighter          │  │
│              │  └─────────────────────────────────┘  │
│              ├─────────────────────────────────────┤
│              │ Tabs: [Matches] [Explain] [Snippets]  │
│              │ ┌─────────────────────────────────┐ │
│              │ │ Match List | Tree View | Export   │ │
│              │ └─────────────────────────────────┘ │
└──────────────┴─────────────────────────────────────┘
```

### **Theming**
- Light/Dark/Auto (follows system)
- Accent color: **indigo-600** (trust + tech)
- Sonner toasts in bottom-right with haptic feedback (mobile)

### **User Flow**
1. Land → empty state: “Try matching an email” with one-click demo
2. Type regex → instant feedback
3. See matches → hover for explanation
4. Click “Copy as Python” → Sonner: “Copied! Ready for pandas”
5. Save or share → “Link copied to clipboard”

## 7. Technical Stack
- **Build Tool**: Vite 6+ (React + TypeScript template)
- **Framework**: React 18 + React Router
- **Language**: TypeScript 5.5+ (strict mode)
- **UI Library**: shadcn/ui (latest) + Sonner
- **Styling**: Tailwind CSS v4 + `@tailwindcss/forms`
- **Editor**: Monaco Editor (via `@monaco-editor/react`)
- **Regex Parsing**: `regexpp` + custom AST enhancer
- **State Management**: Zustand (snippets, history, theme)
- **Persistence**: IndexedDB via `idb-keyval`
- **Testing**: Vitest + React Testing Library + Playwright
- **Deployment**: Vercel (edge-ready)

## 8. Risks and Dependencies
| Risk | Mitigation |
|------|------------|
| Monaco Editor bundle size (~2MB) | Code-split + dynamic import only on mount |
| Regex engine differences across languages | Clear flavor selector + warning badges |
| Parsing very large inputs (>1MB) | Web Worker offloading (v1.1) |
| Sonner/shadcn breaking changes | Pin versions + monitor changelog |

**Out of Scope (MVP)**:
- User accounts / cloud sync
- AI-powered regex generation
- Regex flavor: Java (different unicode handling)
- Real-time collaboration

## 9. Next Steps
### Week 1–2: Foundation
- Scaffold Vite + TypeScript + shadcn/ui + Sonner
- Integrate Monaco with regex grammar
- Real-time matching engine (debounced)

### Week 3–4: Core UX
- Match visualizer + group highlighting
- Explanation tree + tooltips
- Snippet sidebar (50+ patterns)

### Week 5: Export & Share
- Multi-language export
- URL state encoding
- Save/load via IndexedDB

### Week 6: Polish & Launch
- Mobile responsiveness
- PWA manifest
- Accessibility audit
- Public beta on Vercel + Product Hunt

**Launch Goal**: “The Regex101 killer — faster, prettier, and actually understandable.”
