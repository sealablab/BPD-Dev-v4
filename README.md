# forge-v4 Architecture

> **Version:** 4.0.0
> **Last Updated:** 2025-11-10
> **Purpose:** Authoritative naming and repository structure for v4 architecture

---

## 🎯 Design Philosophy

**Key Principle:** Each module is a **standalone, reusable Git repository** that can exist independently OR be composed into monorepos via Git submodules.

---

## 📦 Core Library Modules (Platform-Agnostic)

| Local Directory | GitHub Repository | Submodule Path | Version | Purpose |
|----------------|-------------------|----------------|---------|---------|
| `forge-vhdl-v4/` | [sealablab/forge-vhdl-v4](https://github.com/sealablab/forge-vhdl-v4) | `libs/forge-vhdl` | v4.0.0+ | VHDL component library (digital scaling) |
| `forge-moku-v4/` | [sealablab/moku-models-v4](https://github.com/sealablab/moku-models-v4) | `libs/forge-moku` | v4.2.0+ | Moku platform integration (models, deployment, serialization, AI) |
| `riscure-models-v4/` | [sealablab/riscure-models-v4](https://github.com/sealablab/riscure-models-v4) | `libs/riscure-models` | v4.0.0+ | Probe specifications (template) |

### 📝 Notes

- **Version suffix strategy:** `-v4` suffixed repos created as stable v4.0.0 baseline; non-versioned repos will be created/overwritten later for active development
- **Standalone first:** Each can be cloned/used independently
- **Submodule path convention:** Always under `libs/` in consuming repos

---

## 🗂️ Repository Structure

```
BPD-forge-v4/
├── libs/
│   ├── forge-vhdl/        → https://github.com/sealablab/forge-vhdl-v4
│   ├── forge-moku/        → https://github.com/sealablab/moku-models-v4
│   └── riscure-models/    → https://github.com/sealablab/riscure-models-v4
├── examples/
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
