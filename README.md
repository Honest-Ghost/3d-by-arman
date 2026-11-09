# 3D by Arman (`/3d-by-arman`)

A professional AI agent skill for building and deploying cinematic scroll-driven 3D websites using **Google Flow** and vanilla web technologies.

Whenever you want to build a cinematic site, just type:
```
/3d-by-arman
```
in your AI agent's chat.

---

## 🚀 Quick Install (Single Command Line)

### 1. Antigravity (Google Antigravity IDE)
**PowerShell (Windows):**
```powershell
git clone https://github.com/Honest-Ghost/3d-by-arman.git "$env:USERPROFILE\.gemini\config\skills\3d-by-arman"
```

**Bash (macOS / Linux):**
```bash
git clone https://github.com/Honest-Ghost/3d-by-arman.git ~/.gemini/config/skills/3d-by-arman
```

---

### 2. Claude Code
**Global install (available in all projects):**
```bash
git clone https://github.com/Honest-Ghost/3d-by-arman.git ~/.claude/skills/3d-by-arman
```
*(On Windows PowerShell, use `"$env:USERPROFILE\.claude\skills\3d-by-arman"`)*

**Or per-project in your current workspace:**
```bash
git clone https://github.com/Honest-Ghost/3d-by-arman.git .claude/skills/3d-by-arman
```

---

### 3. Cursor
In your workspace root:
```bash
git clone https://github.com/Honest-Ghost/3d-by-arman.git .cursor/skills/3d-by-arman
```

---

### 4. Codex / Generic Coding Agents
```bash
git clone https://github.com/Honest-Ghost/3d-by-arman.git .agents/skills/3d-by-arman
```

---

## 💡 How It Works

1. **Invoke the Skill:** Type `/3d-by-arman` in your agent chat.
2. **Setup Wizard (Phase 1):** Scans for Google Flow MCP tools (`flow_status`), `ffmpeg`, and `node`.
3. **Design Conversation (Phase 2):** Defines brand goals, assets, and visual feel.
4. **Research & Proposal (Phase 3):** Discovers real customer language and plans hero layout and typography.
5. **Creative Director's Loop (Phase 5):** Establishes the design package and storyboard before any credits are spent.
6. **Hero Generation via Google Flow (Phase 6):**
   - **Start Frame:** Nano Banana model (16:9, **0 credits / free**).
   - **Video Hero:** Google Flow Omni 1.1 Flash (~7-12 credits), Veo 3.1 Fast (~20 credits), or Veo 3.1 Quality (~100 credits) at 1080p.
   - **Multi-segment Chaining:** Seamless chaining using `chain_previous: true` or `flow_continue`.
   - **Supporting Stills:** Generated with `flow_agent_images` (batch 0 credits).
7. **Asset Processing (Phase 7):** Keyframe-dense scrub encoding (`-g 8 -keyint_min 8`) with ffmpeg.
8. **Site Build (Phase 8):** Plain HTML, CSS, and JS with Blob video loading, rAF lerp loop, seek gating, and mobile static fallback.
9. **Self-Testing (Phase 9):** Automated copy review, flick-scroll tests, and worst-frame legibility audit.
10. **One-Command Deploy (Phase 10):** Direct static deploy to Hostinger or your chosen host with speed verification.

---

## 📁 Repository Structure

```
3d-by-arman/
├── SKILL.md                          # Primary agent skill instructions
├── README.md                         # Installation and usage guide
└── references/
    ├── deploy.md                     # Hostinger deploy flow & honest costs
    ├── design-package.md             # Design package template
    ├── ffmpeg-recipes.md             # Keyframe-dense encoding recipes
    ├── prompt-laws.md                # 12 Hero-Video laws & Google Flow prompts
    ├── scrub-pipeline.md             # Scrub hero engineering standard & audits
    └── troubleshooting.md            # Symptom → cause → fix guide
```

---

## 🛠 Prerequisites

- **Google Flow MCP:** Installed and active with Google Flow signed in via Chrome.
- **ffmpeg:** On PATH for video processing.
- **Node.js:** For local preview server.