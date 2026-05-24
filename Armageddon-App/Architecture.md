# Armageddon — System Architecture

> Cross-platform AI desktop assistant built for coding, hacking, and everyday tasks.

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Desktop shell | Electron 42 + Webpack | Cross-platform app container |
| UI framework | React 18 + Tailwind CSS | Component-based UI |
| AI routing | LiteLLM on Railway | Multi-model proxy |
| AI models | Groq (Llama 3.3 70B) / Claude | Language model inference |
| Auth | Firebase Auth | Email/password login |
| Database | Firestore | User data, settings, chat history |
| Knowledge base | GitHub + simple-git | Obsidian vault sync |
| Search | Fuse.js | Fuzzy search through vault notes |

---

## System Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   ELECTRON APP                          │
│                                                         │
│  Renderer Process (React)                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────┐  │
│  │  Login   │  │   Chat   │  │  Vault   │  │  Set  │  │
│  │ Firebase │  │   Area   │  │ Browser  │  │ tings │  │
│  └──────────┘  └────┬─────┘  └────┬─────┘  └───┬───┘  │
│                     │              │             │      │
│  ┌──────────────────┴──────────────┴─────────────┴───┐  │
│  │          Services Layer                           │  │
│  │  aiService.js │ vaultService.js │ firebase       │  │
│  └──────────────────────┬───────────────────────────┘  │
│                         │                               │
│  Main Process (Node.js) │                               │
│  ┌──────────────────────┴───────────────────────────┐  │
│  │     IPC Handlers                                 │  │
│  │  vault:setup │ vault:read │ vault:write          │  │
│  │  simple-git operations                           │  │
│  └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
          │                    │                │
          ▼                    ▼                ▼
   ┌─────────────┐   ┌─────────────────┐  ┌──────────┐
   │  LiteLLM    │   │    Firebase     │  │  GitHub  │
   │  Railway    │   │  Auth+Firestore │  │  Vault   │
   │  Proxy      │   │                 │  │  Repos   │
   └──────┬──────┘   └─────────────────┘  └──────────┘
          │
    ┌─────┴─────┐
    │   Groq    │
    │  Claude   │
    │  OpenAI   │
    └───────────┘
```

---

## Data Flow — Chat Message

```
User types message
      ↓
getVaultContext(message)     ← fuzzy search vault notes
      ↓
inject context into system prompt
      ↓
streamChat() → POST /chat/completions → LiteLLM (Railway)
      ↓
LiteLLM → Groq API → Llama 3.3 70B
      ↓
SSE stream → tokens arrive one by one
      ↓
React state update → token appears in UI
      ↓
onDone() → save to chat history (Firestore)
```

---

## Vault Architecture

### Dev Vault (Marnus's knowledge base)
- GitHub repo: `Marnus-M4/armageddon-vault`
- All users can **read** (injected as AI context)
- Only Marnus can **write**
- Synced via Obsidian Git plugin

### Personal Vault (per user)
- Each user connects their own private GitHub repo
- Stored in Firestore: `users/{uid}/settings.vaultRepoUrl`
- Completely private — only that user can read/write
- AI searches both dev vault + personal vault for context

---

## File Structure

```
Armageddon/
├── src/
│   ├── main.js              ← Electron main process + IPC handlers
│   ├── preload.js           ← Secure IPC bridge
│   ├── renderer.js          ← React entry point
│   ├── App.jsx              ← Root component + auth routing
│   ├── firebase/
│   │   └── config.js        ← Firebase init
│   ├── services/
│   │   ├── aiService.js     ← LiteLLM streaming chat
│   │   └── vaultService.js  ← Vault read/write/search
│   └── components/
│       ├── auth/            ← Login, Register, FirebaseSetup
│       ├── layout/          ← AppShell, Sidebar, Header
│       ├── chat/            ← ChatArea, MessageBubble
│       ├── knowledge/       ← VaultBrowser
│       └── settings/        ← SettingsPanel
├── litellm/
│   ├── Dockerfile           ← Railway deployment
│   └── config.yaml          ← Model configuration
└── vault-starter/           ← Starter knowledge base notes
```

---

## Security Considerations

- Firebase API key is safe to expose (security via Firebase Rules)
- GitHub token stored in Firestore (encrypted at rest by Google)
- LiteLLM master key protects proxy endpoint
- CSP enforced in main.js via session.webRequest
- contextIsolation: true, nodeIntegration: false in renderer
