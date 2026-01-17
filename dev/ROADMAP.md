# ROADMAP: A1111 5-Phase Integration Plan

## Overview
This roadmap guides the integration of AUTOMATIC1111 Stable Diffusion WebUI with a focus on:
- Free GPU hosts for daily experimental use (10+ min/day)
- Model integration (SDXL, Flux, LoRA, ControlNet, CodeFormer, Qwen)
- API abstraction layer for unified model routing
- CI/CD automation and deployment profiles

## Phase 1: Fork & Baseline Setup ✅ IN PROGRESS
**Status:** Setting up foundational docs and repo structure

### Tasks:
- [x] Fork AUTOMATIC1111/stable-diffusion-webui
- [x] Create `dev/ROADMAP.md` (this file)
- [ ] Create `dev/DEV_NOTES.md` - daily workflow & host documentation
- [ ] Create `docs/FREE_GPU_STRATEGY.md` - free GPU provider comparison
- [ ] Add `.gitignore` entries for `models/`, `outputs/`

### Deliverables:
- Forked repo ready for Phase 2
- Documentation structure in place
- Clear baseline for tracking progress

---

## Phase 2: Local & Integrations Setup 🔧 PENDING
**Target Duration:** Week 1-2

### Tasks:
- [ ] Enable & document ControlNet integration
- [ ] Add SDXL checkpoint configuration examples
- [ ] Create LoRA loading documentation (LustiFy, Pony styles)
- [ ] Wire CodeFormer face restoration extension
- [ ] Document Qwen/LLaVA prompt integration (if applicable)
- [ ] Create `integrations/*/models.md` files with checkpoint links
- [ ] Test locally with CPU mode (no GPU needed)

### Deliverables:
- `integrations/*/` docs with model references
- Example configs in `integrations/*/config-example.json`
- Tested locally without GPU

---

## Phase 3: Free GPU Hosts & Automation 🚀 PENDING
**Target Duration:** Week 2-3

### Tasks:
- [ ] Set up AWS SageMaker Studio Lab notebook
- [ ] Create Google Colab notebook (`notebooks/a1111_colab.ipynb`)
- [ ] Document RunPod/other free GPU options
- [ ] Create setup scripts:
  - `dev/scripts/setup_sagemaker.sh`
  - `dev/scripts/setup_colab.sh`
  - `dev/scripts/setup_local.sh`
- [ ] Add per-host gotchas & timeout rules in `dev/hosts/*.md`
- [ ] Create launch profiles for each host

### Deliverables:
- At least 2 daily-usable GPU notebook environments
- Automated setup scripts ready to paste & run
- Host-specific docs with limits & workarounds

---

## Phase 4: API Layer & Model Routing 🔌 PENDING
**Target Duration:** Week 3-4

### Tasks:
- [ ] Create `api-router/server.py` (FastAPI minimal proxy)
- [ ] Implement `api-router/router_config.yaml` with model mappings:
  - SDXL variants
  - Flux checkpoints (if self-hosted)
  - ControlNet configurations
  - CodeFormer toggles
  - LoRA selection fields
- [ ] Build JS client: `api-router/clients/js-client.mjs`
- [ ] Build Python client: `api-router/clients/python-client.py`
- [ ] Test API against local A1111 instance
- [ ] Document OpenAI-compatible wrapper (optional)

### Deliverables:
- Unified REST API wrapping A1111 `/sdapi/v1/*`
- Client libraries ready for dashboard integration
- Example API calls in `api-router/README.md`

---

## Phase 5: Automation, Profiles & Finalization 🎯 PENDING
**Target Duration:** Week 4

### Tasks:
- [ ] Populate `dev/profiles/profile-*.yaml` for each host
- [ ] Create GitHub Actions workflow:
  - `ci.yml` - lint & smoke test
  - `gpu-notebook-sync.yml` (optional) - keep notebooks in sync
- [ ] Update `README.md` with links to `dev/ROADMAP.md`
- [ ] Create `CHANGELOG_DEV.md` - track integration additions
- [ ] Add final gotchas & known limitations
- [ ] Prepare for production or next-phase upgrades

### Deliverables:
- Complete deployment automation
- GitHub Actions CI running on all PRs
- Fully documented daily workflow
- Ready for external contributions

---

## Free GPU Providers Summary

| Provider | Daily Limit | Setup Time | Stability |
|----------|-------------|------------|----------|
| AWS SageMaker Studio Lab | ~4 hours | 10 min | Excellent |
| Google Colab | 1-2 hours | 5 min | Good |
| RunPod (free trial) | Variable | 5 min | Fair |
| Local CPU | Unlimited | N/A | Excellent |

**Strategy:** Use SageMaker + Colab rotating daily; fallback to local CPU for dev/testing.

---

## Model Integration Checklist

- [ ] **SDXL**: Checkpoint in `models/Stable-diffusion/`, API callable
- [ ] **Flux**: (Optional) External API or self-hosted via Together AI
- [ ] **ControlNet**: Extension enabled, `/sdapi/v1/txt2img` accepts controlnet fields
- [ ] **CodeFormer**: Face restoration enabled, API-callable
- [ ] **LoRA** (LustiFy, Pony): Civitai models in `models/Lora/`, loadable via prompt
- [ ] **Qwen**: (Optional) External API or embedded in prompt tools

---

## Current Progress

**Phase 1:** Starting now (Jan 18, 2025)
- [ ] Creating foundational docs
- [ ] Organizing repo structure

**Next:** Moving to Phase 2 once Phase 1 foundation is complete.

---

## Quick Links

- [Upstream AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
- [DEV_NOTES](./DEV_NOTES.md) - Daily workflow guide
- [FREE_GPU_STRATEGY](../docs/FREE_GPU_STRATEGY.md) - GPU provider analysis
- [Integration Docs](../integrations/README.md) - Model-specific setup
