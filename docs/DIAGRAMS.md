# Regex Builder – Visual Architecture & User Flow Diagrams  
**Document Version:** 1.0 (Mermaid.js – Fully Renderable in VS Code, Obsidian, Cursor, GitHub, Notion, HedgeDoc)  
**Compatible With:** Markdown renderers, Mermaid Live Editor: https://mermaid.live  

---

### 1. High-Level Architecture Diagram (C4 Level 2 – Component)

```mermaid
flowchart TB
    subgraph Browser["Browser (Chrome/Firefox/Safari)"]
        direction TB
        subgraph PWA["PWA Shell + Service Worker"]
            App["React App<br/>Vite + TypeScript"]
        end

        subgraph UI["UI Layer – shadcn/ui + Tailwind v4"]
            Header["Header + Theme Toggle"]
            Layout["Resizable 3-Panel Layout"]
            Tabs["Tabs: Matches | Explain | Snippets"]
            Monaco["Monaco Editor<br/>regex grammar"]
            TestPanel["Test Input + File Drop"]
            MatchViz["Match Highlighter"]
            MatchList["Virtualized Match List"]
            Tree["Explanation Tree View"]
            SnippetLib["Snippet Library + Search"]
            Export["Export Dropdown"]
            Sonner["Sonner Toasts"]
        end

        subgraph State["State Management – Zustand + Persistence"]
            ThemeStore["Theme Store"]
            RegexStore["Regex + Flags + Test Text"]
            HistoryStore["Saved Patterns (IndexedDB)"]
            SnippetStore["Snippet Cache"]
        end

        subgraph Core["Core Logic – Pure TS"]
            RegexEngine["useRegexMatch Hook<br/>(debounced + safe RegExp)"]
            Parser["regexpp → AST Parser"]
            Explainer["AST → Human Text"]
            Exporter["Multi-language Export Map"]
            URLCodec["lz-string Encoder/Decoder"]
        end

        subgraph Workers["Web Workers"]
            WW["regex.worker.ts<br/>Large-input matching"]
        end
    end

    %% External
    IndexedDB["IndexedDB<br/>idb-keyval"]
    Vercel["Vercel Edge + Analytics"]
    GitHub["GitHub<br/>snippets.json + Issues"]
    SWCache["Service Worker Cache"]

    %% Connections
    App --> UI
    UI --> State
    State --> Core
    Core --> RegexEngine
    RegexEngine <--> WW
    Core --> Parser
    Parser --> Explainer
    Core --> Exporter
    Core --> URLCodec
    State --> IndexedDB
    SnippetLib --> GitHub
    App --> Vercel
    PWA --> SWCache

    style Browser fill:#1e1e2e,stroke:#cdd6f4,color:#cdd6f4
    style PWA fill:#31324b,stroke:#89b4fa
    style Workers fill:#2b2d42,stroke:#f38ba8
    style IndexedDB fill:#45475a,stroke:#a6e3a1
    style GitHub fill:#45475a,stroke:#fab387
```

---

### 2. User Flow – Main Journey (Onboarding → Share)

```mermaid
flowchart TD
    A[User lands on regexbuilder.app] --> B{First visit?}
    B -->|Yes| C["Welcome Modal + Try Email Demo"]
    B -->|No| D[Load last session from IndexedDB]
    C --> E["One-click loads:<br/>regex: email<br/>test: 100 emails"]
    D --> F[Main UI – Empty or Last Pattern]

    F --> G[User types regex in Monaco]
    G --> H[150ms debounce → useRegexMatch]
    H --> I[Invalid regex → Red squiggle + tooltip]
    H --> J[Valid → Live highlighting in Test Panel]
    J --> K[Matches Tab: List + Groups]
    J --> L[Explain Tab: Tree + English]
    K & L --> M[Hover token → Tooltip]
    J --> N[User adjusts flags g,i,m,s,u,y]

    N --> O[User clicks Snippet → Insert]
    O --> G

    J --> P[User pastes 50k-line log → File Drop]
    P --> Q[Web Worker processes → Streaming results]

    J --> R[Export → Copy as Python / JS / Rust]
    R --> S["Sonner: Copied Rust regex!"]
    
    J --> T[Save Pattern → Name + Tags]
    T --> U[IndexedDB + History Sidebar]

    J --> V[Share → Generate URL]
    V --> W["lz-string compress → #/?s=ABcD..."]
    W --> X[Copy Link → Sonner]

    X --> Y[Recipient opens link → Full state restored]
    Y --> F

    style A fill:#89b4fa,color:#1e1e2e
    style S fill:#a6e3a1,color:#1e1e2e
    style X fill:#fab387,color:#1e1e2e
```

---

### 3. Detailed Component Data Flow (Real-time Matching)

```mermaid
sequenceDiagram
    participant U as User
    participant M as Monaco Editor
    participant H as useRegexMatch Hook
    participant W as Web Worker (optional)
    participant T as Test Panel
    participant ML as Match List
    participant EV as Explanation View
    participant S as Sonner

    U->>M: types /hello\d+/
    M->>H: onChange(value, flags)
    H->>H: debounce 150ms
    H->>W: if input > 50KB → postMessage({regex, text, flags})
    W-->>H: stream matches[]
    alt Small input
        H->>H: new RegExp(regex, flags)
    end
    H-->>T: matches[] + groups[]
    T-->>U: color-coded <mark> highlights
    H-->>ML: virtualized list items
    ML-->>U: click → scroll to match
    H-->>EV: regexpp.parse() → AST
    EV-->>U: Tree + English explanation
    alt Error
        H->>M: monaco.editor.setModelMarkers()
        H->>S: "Invalid regex: Unmatched )"
    end
    alt Success
        H->>S: "42 matches • 3.1ms"
    end
```

---

### 4. State Management Flow (Zustand + Persistence)

```mermaid
stateDiagram-v2
    [*] --> InitialLoad
    InitialLoad --> CheckURL: location.hash
    CheckURL --> LoadFromURL: #/?s=...
    CheckURL --> LoadFromIndexedDB: no hash
    LoadFromURL --> DecodeState
    DecodeState --> LZDecompress: lz-string decompress
    LZDecompress --> RestoreState
    LoadFromIndexedDB --> RestoreState
    RestoreState --> AppReady

    state AppReady {
        [*] --> RegexStore
        [*] --> ThemeStore
        [*] --> HistoryStore
        RegexStore --> SyncURL: on major change
        RegexStore --> AutoSave: every 30s
        HistoryStore --> IndexedDB: savePattern()
        ThemeStore --> LocalStorage: localStorage
    }

    SyncURL --> EncodeState
    EncodeState --> LZCompress: lz-string compress
    LZCompress --> HistoryReplace: history.replaceState

    AppReady --> [*]
```

---

### 5. File Upload → Large Input Pipeline

```mermaid
flowchart LR
    DragFile[User drags 2MB log file] --> DropZone[DropZone Component]
    DropZone --> FileReaderAPI[FileReader.readAsText]
    FileReaderAPI --> ChunkStream[Stream in 64KB chunks]
    ChunkStream --> WebWorker[regex.worker.ts]
    WebWorker --> MessageChannel["postMessage(chunk, index)"]
    WebWorker --> YieldMatches[Yield 100 matches per frame]
    MessageChannel --> MainThread[Comlink / direct handler]
    MainThread --> VirtualList[react-window FixedSizeList]
    VirtualList --> UI[Progressive rendering]
    UI --> Counter["Live: Showing 1–500 of 12,483 matches"]

    style WebWorker fill:#f38ba8,stroke:#f38ba8
    style VirtualList fill:#89ddff,stroke:#89ddff
```

---

### 6. Export System – Multi-language Pipeline

```mermaid
graph TD
    ExportBtn[Export Dropdown] --> Lang[Select Language]
    Lang --> Map["exportMap lookup"]
    Map --> Template[Handlebars-style template]
    Template -->|JS| JS["/pattern/gi"]
    Template -->|Python| Python["r'pattern'"]
    Template -->|Rust| Rust["Regex::new(r'pattern').unwrap()"]
    Template -->|Go| Go["regexp.MustCompile(`pattern`)"]
    JS --> AddComments
    Python --> AddComments
    Rust --> AddComments
    Go --> AddComments
    AddComments["// named groups: (?&lt;email&gt;...)"] --> ClipboardAPI[navigator.clipboard.writeText]
    ClipboardAPI --> SonnerSuccess["Sonner.success('Copied Go regex!')"]
```

---

### 7. Deployment & CI/CD Pipeline

```mermaid
gitGraph
    commit id: "main"
    branch epic/001-foundation
    checkout epic/001-foundation
    commit id: "init vite + shadcn"
    commit id: "add sonner + zustand"
    checkout main
    merge epic/001-foundation tag: "v0.1.0"

    branch epic/003-monaco
    commit id: "monaco + grammar"
    commit id: "real-time engine"
    checkout main
    merge epic/003-monaco tag: "v0.3.0"

    branch epic/009-share
    commit id: "url encoding"
    commit id: "indexeddb"
    checkout main
    merge epic/009-share tag: "v0.7.0"

    branch release/v1.0
    checkout release/v1.0
    commit id: "onboarding tour"
    commit id: "pwa manifest"
    checkout main
    merge release/v1.0 tag: "v1.0.0 Launch"
```
