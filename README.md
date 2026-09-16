# 🧠 AI Agent Skills & Engineering Arsenal

[![Validate Skills](https://github.com/lukaszzychal/ai-agent-skills/actions/workflows/validate.yml/badge.svg)](https://github.com/lukaszzychal/ai-agent-skills/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Created by](https://img.shields.io/badge/Author-Łukasz%20Zychal-orange.svg)](https://github.com/lukaszzychal)

Curated collection of production-grade **AI Agent Skills, Architectural Audits, TDD Scaffolders, Security Guards, and Engineering Rules** for autonomous coding assistants, including **Google Antigravity**, **Gemini CLI**, **Claude Code**, **Cursor**, **Windsurf**, and **GitHub Copilot**.

> **Author & Maintainer:** [Łukasz Zychal](https://github.com/lukaszzychal)

---

## 📚 Overview

Modern AI coding agents become exponentially more effective when instructed with disciplined engineering constraints, architectural principles (DDD, SOLID, CUPID, KISS, YAGNI), and battle-tested verification workflows.

This repository provides **pluggable skills** designed to be **Technology & Language Agnostic** (supporting Python, TypeScript/JavaScript, Rust, Go, PHP, Java, C#) alongside specialized domain toolkits.

---

## 📦 Available Skills (`skills/`)

| Skill Name | Scope | Description |
| :--- | :--- | :--- |
| [`project-audit`](skills/project-audit/SKILL.md) | **Macro / Architecture** | Comprehensive, end-to-end audit of entire codebases: Architecture (DDD, Modular Monolith, Clean Arch), Code Culture (SOLID/CUPID), Test Strategy (TDD, Detroit vs London), DevOps/CI/CD, Security, and Performance without overengineering. |
| [`feature-audit`](skills/feature-audit/SKILL.md) | **Micro / Code Review** | Rapid and focused audit of individual functions, methods, components, or pull request diffs. Provides clean before/after code refactorings and edge-case handling. |
| [`tdd-scaffolder`](skills/tdd-scaffolder/SKILL.md) | **Technology-Agnostic** | TDD test generator and architecture assistant. Guides selection between **Detroit School (Classicist/Black-box)** vs **London School (Mockist/Outside-In)**, enforces strict **AAA / Given-When-Then** structures, and eliminates brittle tests. |
| [`security-guard`](skills/security-guard/SKILL.md) | **Technology-Agnostic** | Pre-commit security sentinel. Detects leaked secrets (API keys, tokens, credentials), audits OWASP Top 10 vulnerabilities (Injection, SSRF, BOLA/IDOR, Path Traversal), and scans dependencies for CVEs. |
| [`api-contract-sync`](skills/api-contract-sync/SKILL.md) | **Technology-Agnostic** | Schema and API contract synchronization (REST, WebSocket, OpenAPI, gRPC). Prevents breaking changes, enforces Single Source of Truth for schemas, and ensures type-safety between backend and frontend. |
| [`perf-doctor`](skills/perf-doctor/SKILL.md) | **Technology-Agnostic** | Performance bottleneck and resource leak analyzer. Detects memory leaks (uncleaned timers, event listeners), N+1 queries, event-loop blocking in async runtimes (FastAPI/Node), and optimizes frontend rendering. |
| [`db-migration-guard`](skills/db-migration-guard/SKILL.md) | **Technology-Agnostic** | Safe database migration assistant. Enforces **Zero-Downtime** deployments via the **Expand-and-Contract** pattern, prevents table locking (`CONCURRENTLY`), and guarantees reversible rollback procedures. |
| [`git-commits`](skills/git-commits/SKILL.md) | **Workflow & Git** | Enforces English commit messages using **Conventional Commits** (`feat:`, `fix:`, `refactor:`, etc.) and automated **SemVer** release tagging (`vX.X.X`). |
| [`tauri-troubleshooter`](skills/tauri-troubleshooter/SKILL.md) | **Desktop Apps (Tauri/Rust)** | Specialized guide for desktop development with Tauri (v1 & v2), Rust IPC (`invoke`/`emit`), system permissions (macOS `Info.plist`, microphone, storage), Next.js static exports, and cross-platform bundling. |
| [`anti-slop-ui`](skills/anti-slop-ui/SKILL.md) | **Frontend / Web Design** | Strict UI/UX auditor & generator. Eliminates AI slop, corporate buzzwords, generic blur blobs, dead anchors, and enforces semantic HTML, a11y (WCAG AA), and flawless mobile-first RWD (375px/768px/1440px). |

---

## 🛠️ Additional Toolkits

- **[`rules/`](rules/)**: Engineering standards and behavioral constraints for coding assistants ([Common Engineering Rules](rules/common-engineering-rules.md)).
- **[`prompts/`](prompts/)**: Ready-to-use battle-tested prompts:
  - [Architecture Review Prompt](prompts/architecture-review.md) – Deep-dive macro/micro architectural review.
  - [Anti-AI-Slop Generator Directive](prompts/anti-ai-slop-generator-prompt.md) – Modular prompt to eliminate AI slop, fake corporate jargon, and generic templates when generating websites.
  - [Web & Mobile UI/UX Audit Prompts](prompts/ai-slop-detect-promt.md) – Multi-perspective audit prompts for desktop, tablet, mobile, screenshots, and localhost.

---

## 🚀 Quick Installation & Setup

### 1. Google Antigravity & Gemini CLI

To install globally for all your projects:
```bash
# Clone the repository
git clone https://github.com/lukaszzychal/ai-agent-skills.git /tmp/ai-agent-skills

# Copy skills to your global Gemini/Antigravity configuration
mkdir -p ~/.gemini/config/skills
cp -r /tmp/ai-agent-skills/skills/* ~/.gemini/config/skills/
```

To install for a specific project:
```bash
mkdir -p .agents/skills
cp -r /tmp/ai-agent-skills/skills/* .agents/skills/
```

### 2. Claude Code / Claude Desktop
Copy relevant skill instructions into your project's `CLAUDE.md` or invoke them as contextual project memory.

### 3. Cursor & Windsurf
Add rules or prompts into `.cursorrules` or configure them as custom slash commands in Cursor Settings.

---

## ⚙️ How AI Agents Discover and Trigger Skills

Skills follow the standard markdown structure with YAML frontmatter:
```yaml
---
name: skill-name
description: "Precise summary of what the skill does and EXACT triggers for invocation."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", ...]
---
```

1. **Semantic Matching:** At session startup, the AI model indexes the `name` and `description` headers. When a user prompt matches the description, the agent automatically reads the full `SKILL.md`.
2. **Explicit Invocation:** You can also trigger any skill manually by mentioning its name:
   > *"Run the `tdd-scaffolder` skill in Detroit style for this service."*
   > *"Execute `security-guard` before we push changes to GitHub."*

---

## 🤝 Contributing & Feedback

Contributions, feature suggestions, and improvements are welcome! Feel free to open an Issue or submit a Pull Request.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE) - created by **Łukasz Zychal**.
