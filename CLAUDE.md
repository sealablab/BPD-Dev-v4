# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Overview

**BPD-Dev-v4** is a monorepo for developing Basic Probe Driver (BPD) custom instruments for Moku platforms (Go/Lab/Pro/Delta). It combines VHDL component libraries, platform models, and probe specifications through a modular Git submodule architecture.

**Key Principle:** Each module is a standalone, reusable Git repository composed into this monorepo via submodules. Each can be cloned and used independently.

---

## Repository Structure

```
BPD-Dev-v4/
├── libs/                          # Platform-agnostic library modules (git submodules)
│   ├── forge-vhdl/                # VHDL component library with AI-assisted development
│   ├── moku-models/               # Moku platform integration (Pydantic models)
│   └── riscure-models/            # Probe specifications (Pydantic models)
├── examples/
│   └── basic-probe-driver/        # Production-ready BPD reference implementation
├── tools/
│   └── decoder/                   # Hierarchical decoder utilities
└── Obsidian/                      # Project notes and documentation (git submodule)
```

---

## Core Submodules

### forge-vhdl (libs/forge-vhdl)
- **Purpose:** Multi-tenant AI-powered VHDL development framework
- **Version:** 3.3.0-multi-tenant
- **Features:**
  - AI-assisted component generation (Claude/Copilot/Cursor support)
  - Progressive testing framework (P1/P2/P3 test levels)
  - VHDL component library for Moku custom instruments
  - CocoTB-based HDL testing
- **Entry Point:** `libs/forge-vhdl/CLAUDE.md` (comprehensive guide for VHDL work)
- **Key Docs:**
  - `libs/forge-vhdl/llms.txt` - Component catalog
  - `libs/forge-vhdl/docs/VHDL_CODING_STANDARDS.md` - Style guide
  - `libs/forge-vhdl/docs/PROGRESSIVE_TESTING_GUIDE.md` - Testing patterns

### moku-models (libs/moku-models)
- **Purpose:** Pydantic models for Moku device deployment, discovery, and configuration
- **Platforms:** Moku:Go (125 MHz), Moku:Lab (500 MHz), Moku:Pro (1.25 GHz), Moku:Delta (5 GHz)
- **Key Functionality:**
  - Device configuration push/pull
  - Routing validation
  - Platform specifications
- **Entry Point:** `libs/moku-models/llms.txt`

### riscure-models (libs/riscure-models)
- **Purpose:** Pydantic models for Riscure FI/SCA probe specifications
- **Supported Probes:** DS1120A (high-power unidirectional EM-FI)
- **Integration:** Works with moku-models for voltage compatibility validation
- **Entry Point:** `libs/riscure-models/llms.txt`

### Obsidian (Obsidian/)
- **Purpose:** Project notes, documentation, and development logs
- **Repository:** [sealablab/BPD-Dev-v4-notes](https://github.com/sealablab/BPD-Dev-v4-notes)
- **Content:** LLM context files, project documentation, templates
- **Entry Point:** `Obsidian/README.md`

---

## Development Workflows

### Working with Submodules

```bash
# Clone repository with all submodules
git clone --recursive <repository-url>

# If already cloned, initialize submodules
git submodule update --init --recursive

# Update submodules to latest
git submodule update --remote

# Work within a submodule
cd libs/forge-vhdl
# Make changes, commit locally
git add .
git commit -m "Update component"
# Push to submodule's remote repository
git push

# Update parent repo to reference new submodule commit
cd ../..
git add libs/forge-vhdl
git commit -m "Update forge-vhdl submodule"
```

### VHDL Component Development

**For VHDL work, always refer to `libs/forge-vhdl/CLAUDE.md` first.** It contains:
- Environment detection (local vs cloud)
- AI-assisted workflows (AI-First vs Engineer requirements)
- 3-agent workflow (component generation → test design → test execution)
- VHDL coding standards
- Progressive testing methodology

**Quick VHDL workflow:**
```bash
cd libs/forge-vhdl

# Detect environment
uv run python .claude/env_detect.py

# Start component development (AI-First)
# In Claude: "I need a PWM generator. Use the AI-First requirements workflow."

# Run tests (P1 level - <20 lines output)
uv run python cocotb_tests/run.py <component>
```

### Python Package Development

**forge-vhdl Python packages:**
```bash
cd libs/forge-vhdl

# Install workspace (includes all Python packages)
uv pip install -e .

# Run Python unit tests
pytest python_tests/

# Run VHDL tests (CocoTB)
uv run python cocotb_tests/run.py <component>
```

**moku-models:**
```bash
cd libs/moku-models

# Install with device operations support
uv pip install -e ".[device]"

# Validate a configuration
python scripts/validate_moku_config.py <config.json>

# Deploy to device
python scripts/push.py -c <config.json> -i <device-ip> -b <bitfile.tar>
```

**riscure-models:**
```bash
cd libs/riscure-models

# Install
uv pip install -e .

# Run tests
pytest tests/
```

---

## Example: Basic Probe Driver

The `examples/basic-probe-driver/` directory contains a **production-ready reference implementation** demonstrating the complete FORGE architecture.

**Key Files:**
- `BPD-RTL.yaml` - Register specification
- `vhdl/FORGE_ARCHITECTURE.md` - Complete 3-layer architecture guide
- `vhdl/CustomWrapper_bpd_forge.vhd` - Moku interface + FORGE wrapper
- `vhdl/BPD_forge_shim.vhd` - Register mapping layer
- `vhdl/src/basic_probe_driver_custom_inst_main.vhd` - FSM implementation
- `vhdl/cocotb_test/` - Progressive CocoTB tests

**Use this as a template** when building new custom instruments.

**Running BPD tests:**
```bash
cd examples/basic-probe-driver/vhdl/cocotb_test

# P1 tests (<20 lines output)
uv run python run.py

# P2 tests (comprehensive)
TEST_LEVEL=P2_INTERMEDIATE uv run python run.py
```

---

## Testing Standards

### Progressive Test Levels (P1/P2/P3)

All VHDL components follow the progressive testing approach:

| Level | Tests | Output | Runtime | Use Case |
|-------|-------|--------|---------|----------|
| **P1** | 2-4 essential | <20 lines | <5 sec | Default - fast iteration, LLM-optimized |
| **P2** | 5-10 + edges | <50 lines | <30 sec | Standard validation |
| **P3** | 15-25 comprehensive | <100 lines | <2 min | Full coverage |

**Golden Rule:** P1 tests must output <20 lines for LLM token efficiency.

### Running Tests

```bash
# From forge-vhdl root
uv run python cocotb_tests/run.py <component>

# Specify test level
TEST_LEVEL=P2_INTERMEDIATE uv run python cocotb_tests/run.py <component>

# Python unit tests
pytest python_tests/
```

---

## VHDL Coding Standards (Quick Reference)

**Mandatory Rules:**

1. **FSM States:** Use `std_logic_vector` constants, NOT enums
   ```vhdl
   -- Correct
   constant STATE_IDLE : std_logic_vector(1 downto 0) := "00";

   -- Wrong (not Verilog-compatible)
   type state_t is (IDLE, ARMED);
   ```

2. **Port Order:** clk, rst_n, clk_en, enable, data, status

3. **Reset Hierarchy:**
   ```vhdl
   if rst_n = '0' then
     -- reset
   elsif clk_en = '1' then
     if enable = '1' then
       -- logic
     end if;
   end if;
   ```

4. **Signal Prefixes:** `ctrl_`, `cfg_`, `stat_`, `dbg_`

**Full standards:** `libs/forge-vhdl/docs/VHDL_CODING_STANDARDS.md`

---

## Common Commands

### Git Operations
```bash
# Update all submodules
git submodule update --remote --merge

# Check submodule status
git submodule status

# Work in specific submodule
cd libs/<submodule>
git checkout main
# make changes
git commit -am "Update"
git push

# Update parent repo
cd ../..
git add libs/<submodule>
git commit -m "Update <submodule> reference"
```

### Python Environment
```bash
# Install uv (fast Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install workspace (forge-vhdl)
cd libs/forge-vhdl
uv pip install -e .

# Install with optional dependencies
uv pip install -e ".[dev]"        # forge-vhdl dev tools
uv pip install -e ".[device]"     # moku-models device ops
```

### VHDL Development
```bash
# Environment detection
uv run python libs/forge-vhdl/.claude/env_detect.py

# Run CocoTB tests
uv run python libs/forge-vhdl/cocotb_tests/run.py <component>

# Run with specific test level
TEST_LEVEL=P2_INTERMEDIATE uv run python cocotb_tests/run.py <component>
```

### Moku Operations
```bash
# Deploy configuration to device
python libs/moku-models/scripts/push.py -c <config.json> -i <ip> -b <bits.tar>

# Pull configuration from device
python libs/moku-models/scripts/pull.py <ip> --output <config.json>

# Validate configuration
python libs/moku-models/scripts/validate_moku_config.py <config.json>
```

---

## Architecture Patterns

### FORGE 3-Layer Architecture

The Basic Probe Driver example demonstrates the standard FORGE architecture:

1. **Layer 1: BRAM Loader** (future integration)
   - Configuration data loading

2. **Layer 2: Shim** (register mapping + synchronization)
   - Unpack Control Registers → `app_reg_*` signals
   - Pack `app_status_*` signals → Status Registers
   - Implement `ready_for_updates` handshaking

3. **Layer 3: Main** (application logic)
   - FSM/application logic
   - Uses `app_reg_*` inputs (NO Control Register knowledge)
   - Generates `ready_for_updates` signal

**Key Principle:** Main app is completely Control Register agnostic, using typed `app_reg_*` signals.

### FORGE Control Scheme (CR0[31:29])

Reserved bits in Control Register 0:
- `forge_ready` - FPGA initialization complete
- `user_enable` - Host software enable
- `clk_enable` - Clock domain enable

**4-condition enable logic:**
```vhdl
app_enable <= forge_ready and user_enable and clk_enable and <app_specific>;
```

---

## Key Documentation Files

**Repository Root:**
- `README.md` - Repository overview and quick start

**forge-vhdl (VHDL Development):**
- `libs/forge-vhdl/CLAUDE.md` - **START HERE for VHDL work**
- `libs/forge-vhdl/llms.txt` - Component catalog
- `libs/forge-vhdl/CONTEXT_MANAGEMENT.md` - Token optimization strategy
- `libs/forge-vhdl/docs/VHDL_CODING_STANDARDS.md` - Complete style guide
- `libs/forge-vhdl/docs/PROGRESSIVE_TESTING_GUIDE.md` - Test patterns

**moku-models (Platform Integration):**
- `libs/moku-models/README.md` - Quick start and API overview
- `libs/moku-models/llms.txt` - LLM quick reference
- `libs/moku-models/DETAILS.md` - Architecture and integration guide
- `libs/moku-models/examples/README.md` - Example configurations

**riscure-models (Probe Specs):**
- `libs/riscure-models/README.md` - Quick start
- `libs/riscure-models/llms.txt` - API and probe specs
- `libs/riscure-models/DETAILS.md` - Technical details

**Basic Probe Driver Example:**
- `examples/basic-probe-driver/README.md` - Reference implementation guide
- `examples/basic-probe-driver/vhdl/FORGE_ARCHITECTURE.md` - Complete architecture spec
- `examples/basic-probe-driver/BPD-RTL.yaml` - Register specification

---

## Tips for Development

1. **For VHDL component work:** Always read `libs/forge-vhdl/CLAUDE.md` first
2. **For new instruments:** Use `examples/basic-probe-driver/` as template
3. **Token efficiency:** Load documentation progressively (see `libs/forge-vhdl/CONTEXT_MANAGEMENT.md`)
4. **Testing:** Start with P1 tests (<20 lines output), escalate to P2/P3 as needed
5. **Submodules:** Commit changes in submodule first, then update parent repo reference

---

**Version:** 4.0.0
**Last Updated:** 2025-11-10
**License:** MIT
