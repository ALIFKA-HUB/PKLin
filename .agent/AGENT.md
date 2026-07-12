# Auto Skill Router — AGENTS.md

> Drop this file into `<project-root>/.agents/AGENTS.md`.
> The agent will automatically detect user intent from every chat message and trigger the appropriate skill(s) — no manual tagging required.

---

## 0. LANGUAGE RULE

**Always match the language of the user.**
- If the user writes in Indonesian, respond in Indonesian.
- If the user writes in English, respond in English.
- If the user mixes languages, follow the dominant language.
- This rule applies to all output: explanations, code comments, commit messages, and artifact summaries.
- Exception: code identifiers (variable names, function names, class names) must always be in English regardless of chat language.

---

## 1. CORE ROUTING LOGIC

Every time the user sends a chat message, follow this sequence **before doing anything else**:

```
Step 1 → Read `using-superpowers` SKILL.md (always-on gate)
Step 2 → Parse user message for INTENT KEYWORDS (Section 3)
Step 3 → Check OPEN FILE CONTEXT (Section 4)
Step 4 → Check PROJECT TYPE CONTEXT (Section 5)
Step 5 → Check for NEGATION words (Section 6)
Step 6 → Check for MANUAL OVERRIDE (Section 7)
Step 7 → Resolve PRIORITY if multiple skills match (Section 8)
Step 8 → Check COLLISION TABLE — never combine colliding skills (Section 10)
Step 9 → If a valid combo exists, use COMBO ORDER (Section 11)
Step 10 → Read the matched SKILL.md file(s), then execute
```

If **no keyword, file context, or project context** matches → fallback to `brainstorming` for clarification.

---

## 2. FUNCTIONAL CATEGORIES

```
Process & Policy         : using-superpowers
Planning & Design        : brainstorming, writing-plans
Execution Orchestration  : subagent-driven-development, executing-plans, dispatching-parallel-agents
Quality & Debugging      : systematic-debugging, test-driven-development (TDD), verification-before-completion
UI/UX Premium            : high-end-visual-design, ui-ux-pro-max, image-to-code
Minimalism               : ponytail, ponytail-audit, ponytail-review, ponytail-gain
Communication Style      : caveman
Output Control           : full-output-enforcement
Platform & Security      : clean-architecture, android-cli, gitlab-integration, playwright-automation, sast-analyzers
```

---

## 3. INTENT KEYWORD MAP

Each row maps user keywords to the skill(s) that should be triggered. Keywords are case-insensitive. Match any keyword in the user's message.

### 3.1 UI/UX & Visual Design

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P1 | "UI", "tampilan", "halaman", "page", "layout", "desain", "design", "warna", "color", "font", "animasi", "animation", "hover", "responsive", "dark mode", "glassmorphism", "gradient", "shadow", "card", "modal", "sidebar", "navbar", "footer", "hero", "section", "component", "komponen" | `ui-ux-pro-max` + `high-end-visual-design` |
| P1 | "landing page", "homepage", "dashboard", "profile page", "settings page" | `ui-ux-pro-max` + `high-end-visual-design` |
| P2 | "wireframe", "mockup", "prototype", "sketch", "gambaran", "konsep visual" | `brainstorming` → `ui-ux-pro-max` |
| P2 | "palet", "palette", "tipografi", "typography", "spacing", "grid system", "design system", "token" | `ui-ux-pro-max` |
| P1 | "convert image", "image to code", "screenshot to code", "gambar jadi kode", "dari gambar" | `image-to-code` |

### 3.2 Planning & Discussion

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P1 | "ide", "idea", "brainstorm", "diskusi", "discuss", "gimana kalau", "what if", "mau bikin", "want to build", "konsep", "concept" | `brainstorming` |
| P1 | "plan", "rencana", "implementasi plan", "task list", "breakdown", "spek", "spec", "specification", "roadmap" | `brainstorming` → `writing-plans` |
| P2 | "arsitektur", "architecture", "struktur folder", "folder structure", "clean arch", "layer", "domain", "hexagonal", "DDD" | `clean-architecture` |

### 3.3 Coding & Execution

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P1 | "implementasi", "implement", "coding", "code", "buat fitur", "build feature", "tambah fitur", "add feature", "develop", "bikin" | `writing-plans` → `subagent-driven-development` + `test-driven-development` |
| P2 | "refactor", "optimasi", "optimize", "clean up", "rapiin", "simplify", "sederhana" | `ponytail` |
| P1 | "parallel", "bagi tugas", "split task", "subagent", "kerja bareng", "concurrent" | `dispatching-parallel-agents` + `subagent-driven-development` |
| P3 | "full output", "jangan potong", "don't truncate", "complete code", "lengkap", "no placeholder" | `full-output-enforcement` |

### 3.4 Debugging & Testing

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P1 | "bug", "error", "gagal", "fail", "rusak", "broken", "crash", "fix", "kenapa", "why", "tidak jalan", "not working", "issue", "problem", "masalah" | `systematic-debugging` |
| P1 | "test", "unit test", "tes", "TDD", "testing", "jest", "vitest", "pytest", "spec" | `test-driven-development` |
| P1 | "e2e", "end to end", "playwright", "automasi browser", "browser automation", "selenium" | `playwright-automation` |
| P1 | "udah bener?", "done?", "cek", "check", "verifikasi", "verify", "pastiin", "confirm", "sudah selesai", "is it done", "validate" | `verification-before-completion` |

### 3.5 Security & DevOps

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P1 | "security", "keamanan", "vulnerability", "celah", "inject", "XSS", "SQL injection", "CSRF", "auth", "sanitize", "escape" | `sast-analyzers` |
| P1 | "gitlab", "merge request", "MR", "pipeline", "CI/CD", "deploy", "deployment", "glab" | `gitlab-integration` |
| P1 | "android", "APK", "emulator", "SDK", "gradle", "kotlin", "mobile app" | `android-cli` |

### 3.6 Communication & Compression

| Priority | Keywords | Trigger Skill(s) |
|----------|----------|-------------------|
| P2 | "singkat", "brief", "caveman", "hemat token", "less token", "be brief", "ringkas" | `caveman` |
| P2 | "minimal", "YAGNI", "potong yang ga perlu", "over-engineered", "too complex", "kebanyakan", "bloat" | `ponytail` |
| P3 | "audit repo", "audit kode", "code audit" | `ponytail-audit` |
| P3 | "review changes", "review perubahan" | `ponytail-review` |
| P3 | "impact", "gain", "berapa hemat" | `ponytail-gain` |

---

## 4. OPEN FILE CONTEXT DETECTION

If the user has files open in their editor, use the file extension to **bias** skill selection (not override — bias adds weight, user keywords still take priority):

| Open File Extension | Bias Toward |
|---------------------|-------------|
| `.css`, `.scss`, `.less`, `.styled.ts`, `.styled.js` | `ui-ux-pro-max` + `high-end-visual-design` |
| `.html`, `.jsx`, `.tsx`, `.vue`, `.svelte`, `.astro` | `ui-ux-pro-max` + `high-end-visual-design` |
| `.test.ts`, `.test.js`, `.spec.ts`, `.spec.js`, `*.test.*` | `test-driven-development` |
| `.yml`, `.yaml`, `.gitlab-ci.yml`, `Jenkinsfile` | `gitlab-integration` |
| `.gradle`, `.kt`, `.java`, `AndroidManifest.xml` | `android-cli` |
| `.py`, `.go`, `.rs`, `.rb`, `.php` | `clean-architecture` |
| `Dockerfile`, `docker-compose.yml`, `.env` | `gitlab-integration` |
| `playwright.config.*`, `*.e2e.*` | `playwright-automation` |

**Rule:** If the user's message has no clear intent keywords but they have a `.css` file open and say something like "make this better", bias toward `ui-ux-pro-max`.

---

## 5. PROJECT TYPE AUTO-DETECTION

On first interaction in a workspace, scan the project root for marker files to set a **project profile**. This profile persists for the entire conversation.

| Marker File(s) | Project Type | Default Bias |
|-----------------|-------------|--------------|
| `next.config.*`, `nuxt.config.*`, `vite.config.*`, `astro.config.*` | Web Frontend | `ui-ux-pro-max`, `high-end-visual-design` |
| `build.gradle`, `settings.gradle`, `AndroidManifest.xml` | Android | `android-cli` |
| `package.json` (with test scripts) | Node.js | `test-driven-development` |
| `.gitlab-ci.yml` | GitLab CI | `gitlab-integration` |
| `requirements.txt`, `pyproject.toml`, `setup.py` | Python Backend | `clean-architecture`, `sast-analyzers` |
| `go.mod` | Go Backend | `clean-architecture` |
| `Cargo.toml` | Rust | `clean-architecture` |
| `playwright.config.*` | E2E Testing | `playwright-automation` |

**Rule:** Project type bias is the **lowest priority**. It only activates when both keyword match and file context are absent or ambiguous.

---

## 6. NEGATION HANDLING

If the user's message contains negation words **before or near** a keyword, **do NOT trigger** the associated skill.

### Negation Patterns:
| Pattern | Example | Effect |
|---------|---------|--------|
| "jangan" / "don't" / "no" + keyword | "jangan terlalu mewah" | Do NOT trigger `high-end-visual-design` |
| "tanpa" / "without" + keyword | "tanpa animasi" | Do NOT apply animation rules from `high-end-visual-design` |
| "ga usah" / "skip" + keyword | "ga usah test dulu" | Do NOT trigger `test-driven-development` |
| "bukan" / "not" + keyword | "bukan UI, backend aja" | Do NOT trigger UI skills |
| "simpel aja" / "keep it simple" | "bikin simpel aja" | Trigger `ponytail` instead of premium UI skills |

**Rule:** Negation within 5 words of a keyword cancels that keyword's skill trigger. Scan for negation BEFORE matching keywords.

---

## 7. MANUAL OVERRIDE

If the user **explicitly names a skill**, bypass all auto-routing and use that skill directly.

### Override Patterns:
```
"pakai {skill-name}"        → use that skill
"use {skill-name}"          → use that skill
"trigger {skill-name}"      → use that skill
"switch to {skill-name}"    → use that skill
"only {skill-name}"         → use that skill, ignore all others
"stop {skill-name}"         → deactivate that skill for this conversation
"reset"                     → clear all active skill overrides
```

**Rule:** Manual override has the **highest priority** — it overrides keywords, file context, project context, and even collision rules (at the user's own risk).

---

## 8. PRIORITY RESOLUTION

When multiple skills match from different sources, resolve using this priority order (highest to lowest):

```
P0 — Manual Override (user explicitly names a skill)
P1 — Primary Keyword Match (exact intent keyword detected)
P2 — Secondary Keyword Match (related/weaker keyword detected)
P3 — Tertiary Keyword Match (peripheral keyword detected)
P4 — Open File Context Bias
P5 — Project Type Bias
P6 — Fallback (brainstorming)
```

**When two skills have the SAME priority:**
1. Check if they form a valid COMBO (Section 11) → use combo order.
2. Check if they COLLIDE (Section 10) → ask user which one to use.
3. If neither → run both in the order listed in the keyword table.

---

## 9. MULTI-TURN PHASE TRACKING

Within a single conversation, track the current **development phase** and use it to inform skill selection on ambiguous messages.

### Phase Transitions:
```
[IDLE]
  ↓ user starts discussing ideas
[PLANNING]         → bias: brainstorming, writing-plans
  ↓ user approves plan / starts coding
[IMPLEMENTING]     → bias: subagent-driven-development, TDD, clean-architecture
  ↓ user encounters bug / error
[DEBUGGING]        → bias: systematic-debugging
  ↓ user asks to verify / check
[VERIFYING]        → bias: verification-before-completion, playwright-automation
  ↓ user asks to deploy / merge
[SHIPPING]         → bias: gitlab-integration, sast-analyzers
  ↓ done or new topic
[IDLE]
```

**Rules:**
- Phase transitions are inferred from user messages, not declared explicitly.
- The phase **biases** skill selection but does NOT override explicit keyword matches.
- If the user starts a completely new topic (e.g., switches from backend to UI), reset to `[IDLE]` and re-detect.
- If ambiguous, state the detected phase to the user: *"It looks like we're in the debugging phase — I'll use systematic-debugging."*

---

## 10. COLLISION TABLE — NEVER COMBINE

These skill pairs **must never run together** in the same task. If auto-routing detects both, **ask the user** which approach they prefer.

| Skill A | Skill B | Reason |
|---------|---------|--------|
| `caveman` | `ponytail` | Agent becomes dismissive. Terse prose + aggressive code deletion = incoherent reviews and lost features. |
| `high-end-visual-design` | `ponytail` | Infinite loop. Agent builds premium UI, then strips it as over-engineered. Wastes tokens and time. |
| `sast-analyzers` | `ponytail` | Security gap. Ponytail deletes validation/logging/error-handling code that sast-analyzers requires for safety. |
| `subagent-driven-development` | `executing-plans` | Workflow conflict. SDD dispatches subagents; executing-plans runs sequentially in a single session. Pick one. |
| `caveman` | `writing-plans` | Plans require detailed, structured prose. Caveman compresses everything, producing incomplete/unreadable plans. |
| `caveman` | `high-end-visual-design` | Design specs need rich descriptions. Caveman's terse output can't convey visual nuance. |
| `ponytail` | `full-output-enforcement` | Philosophical clash. Ponytail wants less code; full-output-enforcement demands complete, unabridged output. |

### Collision Resolution Prompt:
When a collision is detected, ask the user:
> "I detected keywords for both `{skill-a}` and `{skill-b}`, but these skills conflict with each other. Which approach do you prefer?"
> - Option A: `{skill-a}` — {one-line description}
> - Option B: `{skill-b}` — {one-line description}

---

## 11. EFFECTIVE COMBOS

When these skill combinations are triggered, execute them in the specified order.

| Scenario | Skill Chain | Description |
|----------|-------------|-------------|
| New Feature | `brainstorming` → `writing-plans` | Explore requirements, then produce structured implementation plan. |
| Coding | `subagent-driven-development` + `test-driven-development` | Distribute tasks to subagents, each writing tests first (red-green-refactor). |
| UI/UX | `ui-ux-pro-max` + `high-end-visual-design` | Pull design tokens and references, then apply premium visual rules. |
| UI from Image | `image-to-code` → `ui-ux-pro-max` + `high-end-visual-design` | Convert reference image to code, then polish with design system. |
| Fix Bug | `systematic-debugging` → `test-driven-development` → `verification-before-completion` | Find root cause → write regression test → prove it passes. |
| Minimalist | `ponytail` + `caveman` | Strip unnecessary code + compress communication tokens. |
| Full Build | `brainstorming` → `writing-plans` → `dispatching-parallel-agents` + `subagent-driven-development` + `test-driven-development` → `verification-before-completion` | Complete lifecycle: ideate → plan → parallel implement with TDD → verify. |
| Security Audit | `sast-analyzers` → `systematic-debugging` → `verification-before-completion` | Scan for vulns → debug/fix each → verify fixes pass. |
| Code Review | `ponytail-review` → `ponytail-gain` | Review changes for bloat → measure impact of removals. |
| Repo Health | `ponytail-audit` → `ponytail-gain` | Audit entire repo → show deletion scoreboard. |

### Combo Arrow Semantics:
- `→` means **sequential**: finish the first skill before starting the next.
- `+` means **parallel/concurrent**: both skills inform the same task simultaneously.

---

## 12. DISCUSSION MODE & EFFORT LEVELS

These skills activate a **discussion/exploration mode** rather than direct code generation. Understand their effort cost before triggering.

| Skill | Effort | What Happens |
|-------|--------|-------------|
| `brainstorming` | 🔴 High | Intensive back-and-forth Q&A. Agent asks clarifying questions, explores edge cases, produces a spec file. Expect 5-15 exchanges before output. |
| `writing-plans` | 🔴 High | Agent produces a detailed implementation plan with file-by-file breakdown, verification steps, and open questions. Requires user approval before proceeding. |
| `ponytail` | 🟡 Medium | Agent reviews code/features against YAGNI principles. Suggests deletions and simplifications. May challenge user's feature requests. |
| `ponytail-audit` | 🟡 Medium | Full-repo scan for over-engineering. Produces a prioritized list of what can be deleted. |
| `ui-ux-pro-max` | 🟢 Low | Pulls design references (50 styles, 21 palettes, 50 font pairings) via internal scripts. Minimal interaction needed. |
| `caveman` | 🟢 Low | Auto-compresses all text output. No extra interaction — just shorter messages. |
| `full-output-enforcement` | 🟢 Low | Silently ensures no code truncation. No interaction — just longer, complete output. |

---

## 13. USING-SUPERPOWERS INTEGRATION

The `using-superpowers` skill is the **meta-gate** that must run at the start of every conversation. It establishes:
- How to discover and load other skills.
- The requirement to read SKILL.md before using any skill.
- The prohibition against responding without first checking applicable skills.

**Rule:** On the very first user message in a conversation:
1. Silently load `using-superpowers`.
2. Then proceed with the routing logic in Section 1.
3. Do NOT mention `using-superpowers` to the user — it is an internal process.

---

## 14. EDGE CASES

### 14.1 Empty or Vague Messages
If the user sends something like "hey", "hi", "lanjut", "continue", or any message with no detectable intent:
- If a phase is active (Section 9), continue in that phase.
- If no phase is active, respond normally without triggering any skill.

### 14.2 Multiple Intents in One Message
If the user sends a compound message like *"fix the login bug and then make the dashboard UI look premium"*:
1. Split into sub-intents: `[fix login bug]` + `[dashboard UI premium]`.
2. Route each sub-intent independently.
3. Execute sequentially: resolve the bug first, then work on UI.

### 14.3 Contradictory Intent
If the user says something like *"make it minimal but also premium looking"*:
- Detect the contradiction (minimal → `ponytail`, premium → `high-end-visual-design`).
- These collide (Section 10). Ask the user to clarify.

### 14.4 Skill Not Installed
If auto-routing maps to a skill that is not installed:
- Skip that skill silently.
- If a combo requires it, run the remaining skills in the combo.
- Inform the user only if the missing skill is critical to their request.

---

## 15. HOW TO USE THIS FILE

1. Copy this file to `<your-project-root>/.agents/AGENTS.md`.
2. Open a new conversation in Antigravity with the workspace set to your project.
3. Chat naturally — the agent will auto-route to the right skills.

### Examples:

```
User: "I want to build a dashboard with charts and dark mode"
  → Keywords detected: "build", "dashboard", "charts", "dark mode"
  → Matched skills: ui-ux-pro-max + high-end-visual-design
  → Agent reads both SKILL.md files
  → Output: Premium dashboard design with curated palette, typography, and animations

User: "There's a bug in the payment form, it crashes on submit"
  → Keywords detected: "bug", "crashes"
  → Phase transition: [IDLE] → [DEBUGGING]
  → Matched skill: systematic-debugging
  → Agent reads SKILL.md and follows root-cause analysis protocol

User: "Plan out the auth system for this app"
  → Keywords detected: "plan", "auth system"
  → Matched skills: brainstorming → writing-plans
  → Agent runs brainstorming Q&A, then produces implementation plan

User: "Use ponytail"
  → Manual override detected
  → Bypasses all auto-routing
  → Agent reads ponytail SKILL.md and applies minimalism rules

User: "Make this simpler but keep it secure"
  → Keywords: "simpler" → ponytail, "secure" → sast-analyzers
  → Collision detected! (ponytail + sast-analyzers)
  → Agent asks: "These skills conflict. Prefer minimal code or full security coverage?"
```
## 16. AUTO CONTEXT DUMP (ANTI-AMNESIA SYSTEM)

When the context window is approaching its maximum capacity, the agent **must automatically** create a super-detailed conversation summary file — without the user asking for it.

### 16.1 Trigger Conditions

The agent MUST create a context dump when **any** of these conditions are detected:

| Condition | How to Detect |
|-----------|---------------|
| Checkpoint summary appears | The system message says "The earlier parts of this conversation have been truncated" |
| Agent notices own memory gaps | Agent cannot recall details that were discussed earlier |
| Conversation exceeds ~80 chat exchanges | Count approximate number of user messages |
| Large code outputs consumed significant tokens | Multiple code generations (500+ lines each) in one conversation |
| User switches to a model with smaller context window | e.g., from Gemini 1M to Claude 200K |

### 16.2 Auto-Dump Behavior

When a trigger condition is met:

1. **Immediately** create/update the dump file — do NOT wait for the user to ask.
2. **Notify the user** briefly: *"Context window is getting full. I've saved a detailed summary to `context_dump.md`."*
3. **Continue working** normally after the dump — do not stop or pause.
4. **Update the dump** if the conversation continues significantly after the first dump.

### 16.3 Dump File Location & Naming

```
<project-root>/.agents/context_dumps/
└── <YYYY-MM-DD>_<short-topic>.md

Example:
.agents/context_dumps/
├── 2026-07-12_dashboard-ui-redesign.md
├── 2026-07-13_auth-system-implementation.md
└── 2026-07-14_payment-bug-fix.md
```

If no workspace is active, save to the artifact directory instead:
```
<appDataDir>/brain/<conversation-id>/context_dump.md
```

### 16.4 Dump File Template

The dump file MUST follow this exact structure. Every section is **mandatory** — do not skip any section, even if empty (write "None" if nothing applies).

```markdown
# Context Dump — [Topic/Task Title]

Generated: [YYYY-MM-DD HH:MM timezone]
Conversation ID: [conversation-id]
Model Used: [model name]
Workspace: [project path]
Phase at Dump: [IDLE/PLANNING/IMPLEMENTING/DEBUGGING/VERIFYING/SHIPPING]

---

## 1. OBJECTIVE
What the user is trying to accomplish. Include the original request and any refined scope.

## 2. KEY DECISIONS MADE
Numbered list of every decision agreed upon during the conversation.
Include WHO decided (user or agent) and WHY.

1. [Decision] — Decided by [user/agent]. Reason: [why]
2. ...

## 3. TECHNICAL SPECIFICATIONS
All technical details that were discussed and agreed upon:
- Architecture / patterns chosen
- Tech stack / frameworks / libraries
- Color palettes, fonts, spacing (if UI work)
- API contracts, data models, schemas
- File structure / naming conventions
- Environment variables, configs

## 4. FILES CREATED / MODIFIED
Full list of every file that was created or modified, with a one-line summary of what changed.

| Action | File Path | Summary |
|--------|-----------|---------|
| CREATED | src/components/Dashboard.tsx | Main dashboard component with chart grid |
| MODIFIED | src/styles/global.css | Added dark mode variables and card styles |
| DELETED | src/old/legacy-dashboard.js | Removed deprecated dashboard |

## 5. CODE SNIPPETS & PATTERNS
Important code patterns, utility functions, or architectural decisions that should be preserved.
Include the actual code — not just descriptions.

## 6. BUGS FOUND & FIXED
| Bug | Root Cause | Fix Applied | File |
|-----|-----------|-------------|------|
| Form submit crashes | Missing null check on payload | Added optional chaining | src/api/submit.ts |

## 7. BUGS / ISSUES STILL OPEN
| Bug/Issue | Status | Notes |
|-----------|--------|-------|
| Chart tooltip misaligned on mobile | Not started | Low priority per user |

## 8. TODO — REMAINING TASKS
Ordered list of what still needs to be done.

- [ ] Task 1
- [ ] Task 2
- [x] Task 3 (completed)

## 9. USER PREFERENCES & STYLE
Any preferences the user expressed during the conversation:
- Communication style (brief, detailed, etc.)
- Design preferences (dark mode, minimal, premium, etc.)
- Code style preferences (functional, OOP, naming conventions, etc.)
- Language preference (Indonesian, English, mixed)

## 10. CONVERSATION HIGHLIGHTS
Key exchanges that shaped the direction of work. Paraphrase the most important user messages and agent responses.

### Exchange 1: [Topic]
- User said: "..."
- Agent responded: "..."
- Outcome: [what was decided/done]

## 11. SKILLS USED
| Skill | When Used | Outcome |
|-------|-----------|---------|
| brainstorming | Chat 1-5 | Produced feature spec |
| ui-ux-pro-max | Chat 6-12 | Generated design system |

## 12. CONTEXT FOR NEXT CONVERSATION
A prose summary written specifically for the next agent/conversation to pick up exactly where this one left off. Write as if briefing a colleague who will continue the work.
```

### 16.5 Quality Rules for Dumps

1. **Be exhaustive, not summarized.** Include specific values: color hex codes, font names, pixel sizes, variable names — not "we chose a dark theme."
2. **Include actual code.** If a pattern or function was agreed upon, paste the code — don't describe it.
3. **Preserve the user's exact words** for key decisions. Quote them.
4. **Never skip a section.** Write "None" if empty.
5. **File paths must be absolute or relative to project root.** No ambiguous references.
6. **Update, don't duplicate.** If a dump already exists for this conversation, update it instead of creating a new one.

### 16.6 How to Use Dumps in a New Conversation

When starting a new conversation, if the user says anything like:
- "lanjutin yang kemarin"
- "continue from last session"
- "baca context dump"
- "load previous context"

The agent should:
1. Look for dump files in `.agents/context_dumps/`
2. Read the most recent one (or the one the user specifies)
3. Load all context from the dump
4. Confirm to the user: *"Loaded context from [filename]. Resuming from [phase]. Next task: [first TODO item]."*

### 16.7 Auto-Dump Reminder

If the conversation is very long and NO dump has been created yet, the agent should **proactively suggest** it:

> "This conversation is getting long. Want me to save a detailed context dump so nothing gets lost?"

If the user says yes (or anything affirmative), create the dump immediately.
If the user says no, respect it but remind again after 20 more exchanges.
