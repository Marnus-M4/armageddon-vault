# Armageddon — Build Roadmap

---

## ✅ Phase 1 — Foundation (COMPLETE)
**Goal:** Working Electron app with auth and UI shell

- [x] Electron Forge + Webpack project setup
- [x] React 18 + Tailwind CSS (dark cyberpunk theme)
- [x] Firebase Auth — email/password login + registration
- [x] Firestore — user profile creation on register
- [x] App shell — sidebar, header, chat area layout
- [x] Multi-device ready — Firebase session persists
- [x] CSP configured in main.js

---

## ✅ Phase 2 — AI Brain (COMPLETE)
**Goal:** Real AI responses via LiteLLM proxy

- [x] LiteLLM deployed to Railway (Docker)
- [x] Groq integration — Llama 3.3 70B, Llama 3.1 8B, Mixtral 8x7B
- [x] Claude slot pre-wired (uncomment when Anthropic key ready)
- [x] Streaming responses — tokens appear as they arrive
- [x] Model selector in chat UI
- [x] System prompt — Armageddon persona
- [x] Conversation history maintained per session

---

## 🔄 Phase 3 — Knowledge Base (IN PROGRESS)
**Goal:** Obsidian vault synced via GitHub, injected into AI context

- [x] IPC handlers in main.js — vault:setup, vault:list, vault:read, vault:write
- [x] Preload bridge — exposes vault API to renderer
- [x] vaultService.js — Fuse.js fuzzy search + context injection
- [x] VaultBrowser UI — file tree, note viewer, note editor
- [x] SettingsPanel — GitHub token + repo URL config
- [x] Auto-sync on startup (pull latest notes)
- [x] Vault context injected into every chat message
- [ ] Dev vault (Marnus's) — read-only for all users
- [ ] Personal vault per user — private, read/write
- [ ] Obsidian Git plugin linked to armageddon-vault repo

---

## 🔜 Phase 4 — Security Intelligence
**Goal:** OWASP data + autonomous recon agent

- [ ] OWASP data feed integration (CVE/NVD API)
- [ ] GitHub Security Advisory API
- [ ] Recon agent — nmap/shodan integration via tool use
- [ ] Security findings panel UI
- [ ] Claude tool use — structured tool calls

---

## 🔜 Phase 5 — Autonomous Agent Loop
**Goal:** AI that learns and acts on its own

- [ ] Claude tool use framework
- [ ] Tools: read_vault, write_vault, search_web, run_code, search_github
- [ ] Agentic loop — AI plans and executes multi-step tasks
- [ ] Background learning agent — periodic vault updates
- [ ] Self-updating memory — AI summarises and saves conversations
- [ ] Agent UI — task queue, step-by-step progress display

---

## 🔜 Phase 6 — Multi-Device Sync Polish
**Goal:** Seamless experience across all devices

- [ ] Chat history saved to Firestore per user
- [ ] Device registration + session logging
- [ ] Firestore real-time listeners — live sync across devices
- [ ] Settings sync across devices
- [ ] Notifications for background agent tasks

---

## 🔜 Phase 7 — Production & Distribution
**Goal:** Packaged, distributable app

- [ ] App icon + branding
- [ ] Electron Forge makers — Windows (Squirrel), Mac (dmg), Linux (deb)
- [ ] Auto-updater
- [ ] Production CSP hardening
- [ ] Remove DevTools from production builds
- [ ] Code signing (optional)
