# BPD-Dev-v4

> **Version:** 4.0.0
> **Last Updated:** 2025-11-10
> **Purpose:** Authoritative naming and repository structure for v4 architecture

---
This is the current home of ongoing **Basic Probe Driver** work
## 🎯 Design Philosophy

**Key Principle:** Each module is a **standalone, reusable Git repository** that can exist independently OR be composed into monorepos via Git submodules.

---

## 📦 Core Library Modules (Platform-Agnostic)

| Local Directory | GitHub Repository | Submodule Path | Version | Purpose |
|----------------|-------------------|----------------|---------|---------|
| `forge-vhdl-v4/` | [sealablab/forge-vhdl-v4](https://github.com/sealablab/forge-vhdl-v4) | `libs/forge-vhdl` | v4.0.0+ | VHDL component library (digital scaling) |
| `moku-models-v4/` | [sealablab/moku-models-v4](https://github.com/sealablab/moku-models-v4) | `libs/moku-models` | v4.2.0+ | Moku platform integration (models, deployment, serialization, AI) |
| `riscure-models-v4/` | [sealablab/riscure-models-v4](https://github.com/sealablab/riscure-models-v4) | `libs/riscure-models` | v4.0.0+ | Probe specifications (template) |

## 📝 Documentation Module

| Local Directory | GitHub Repository | Submodule Path | Version | Purpose |
|----------------|-------------------|----------------|---------|---------|
| `BPD-Dev-v4-notes/` | [sealablab/BPD-Dev-v4-notes](https://github.com/sealablab/BPD-Dev-v4-notes) | `Obsidian` | - | Project notes and documentation |

### 📝 Notes

- **Version suffix strategy:** `-v4` suffixed repos created as stable v4.0.0 baseline; non-versioned repos will be created/overwritten later for active development
- **Standalone first:** Each can be cloned/used independently
- **Submodule path convention:** Always under `libs/` in consuming repos

---

## 🗂️ Repository Structure

```
BPD-Dev-v4/
├── libs/
│   ├── forge-vhdl/        → https://github.com/sealablab/forge-vhdl-v4
│   ├── moku-models/       → https://github.com/sealablab/moku-models-v4
│   └── riscure-models/    → https://github.com/sealablab/riscure-models-v4
├── examples/
└── Obsidian/              → https://github.com/sealablab/BPD-Dev-v4-notes
```

---

## 🚀 Quick Start

Clone this repository with all submodules:

```bash
git clone --recursive git@github.com:sealablab/forge-v4-monotop.git
```

Or if already cloned, initialize submodules:

```bash
git submodule update --init --recursive
```
