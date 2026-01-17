# DEV_NOTES: Daily Workflow & Host Setup Guide

## Quick Start: Pick Your Host & Go

Every day, pick one free GPU host from below, spin it up, and work for 10+ minutes. No credit card required for any of them.

### Daily Workflow Loop

1. **Pick Host**: Choose SageMaker or Colab (see "Free Hosts" section)
2. **Clone & Setup**: Run the corresponding `setup_*.sh` script
3. **Launch A1111**: Start webui with `python launch.py --listen`
4. **Expose**: Use ngrok or cloud tunnel to expose `localhost:7860`
5. **Connect Dashboard**: Point your dashboard to the exposed URL
6. **Work**: Test image generation, LoRAs, ControlNet for 10+ min
7. **Shutdown**: Terminate to free up GPU quota

---

## Free Hosts: Setup & Limits

### 1. AWS SageMaker Studio Lab (BEST)

**URL:** https://studiolab.sagemaker.aws/

**Daily Quota:** ~4 hours compute (GPU)

**Setup Steps:**
```bash
# In SageMaker notebook (Jupyter)
!git clone https://github.com/<your-github>/stable-diffusion-webui.git
%cd stable-diffusion-webui
!bash dev/scripts/setup_sagemaker.sh
!python launch.py --listen --xformers --opt-split-attention
```

**Gotchas:**
- Idle timeout: ~1 hour of inactivity = auto-stop
- GPU auto-stops after 4 hours total daily usage
- Restart notebook to reset the timer

**Expose URL:**
```bash
# In notebook after A1111 starts
!pip install pyngrok
from pyngrok import ngrok
ngrok.set_auth_token('YOUR_NGROK_TOKEN')
public_url = ngrok.connect(7860)
print(public_url)
```

---

### 2. Google Colab (BACKUP)

**URL:** https://colab.research.google.com/

**Daily Quota:** 1-2 hours (GPU varies by region/day)

**Setup Steps:**
1. Open `notebooks/a1111_colab.ipynb` in Colab
2. Run cells sequentially
3. Wait ~5 min for install

**Gotchas:**
- GPU randomly stops after 1-2 hours
- Must restart runtime if timeout
- Sometimes GPU not available (fall back to SageMaker/Local CPU)

**Expose URL:**
- Colab provides CloudFlare tunnel automatically
- Look for "share" link in notebook output

---

### 3. Local CPU (ALWAYS AVAILABLE)

**Setup:**
```bash
cd stable-diffusion-webui
bash dev/scripts/setup_local.sh
python launch.py
```

**Limits:**
- VERY slow (CPU inference 30-60 sec per image on typical machine)
- Good for testing API, LoRA loading, ControlNet configs
- Avoid for actual image generation; use for dev/debugging only

---

## Integration Checklist: Verify Before Dev

### ControlNet
- [ ] Run: `curl http://localhost:7860/sdapi/v1/controlnet/detect`
- [ ] If 404, ControlNet extension not loaded; see `integrations/controlnet/README`
- [ ] Test payload: `integrations/controlnet/api-examples.json`

### CodeFormer
- [ ] Toggle in UI: `Extras` → `CodeFormer strength` > 0
- [ ] API: Include `"restore_faces": true` in `/sdapi/v1/txt2img` payload

### LoRA (LustiFy, Pony)
- [ ] Download from Civitai to `models/Lora/`
- [ ] Reload UI (web button top-right)
- [ ] Select in LoRA dropdown or embed in prompt: `<lora:lustify:0.8>` 

### SDXL
- [ ] Checkpoint in `models/Stable-diffusion/`
- [ ] Select from "Stable Diffusion checkpoint" dropdown
- [ ] API call includes `model: "model_name.safetensors"`

---

## Troubleshooting

### WebUI won't start
```bash
# Check port conflict
lsof -i :7860
# If occupied, kill it
kill -9 <PID>
```

### Out of memory (CUDA)
- Add `--lowvram` or `--medvram` flag
- Reduce batch size: `--batch-size 1`

### ControlNet 404
- Ensure extension cloned: `ls extensions/sd-webui-controlnet/`
- If missing:
  ```bash
  cd extensions
  git clone https://github.com/Mikubill/sd-webui-controlnet.git
  cd ..
  ```

### API returns wrong model
- Verify model dropdown in UI matches API request
- API does NOT auto-switch models; must match UI selection

---

## Daily Checklist (10+ min session)

- [ ] Pick host (SageMaker or Colab)
- [ ] Start webui
- [ ] Expose URL (ngrok or Colab link)
- [ ] Test SDXL generation (1-2 prompts)
- [ ] Test ControlNet (with a simple prompt)
- [ ] Test CodeFormer (face restoration on generated image)
- [ ] Load a LoRA and test prompt
- [ ] Shutdown & free up quota

---

## Next: See dev/hosts/ for per-host docs

- `local.md` - Local CPU setup details
- `sagemaker-lab.md` - Full SageMaker guide
- `colab.md` - Colab notebook tips
- `other-free-gpu.md` - RunPod / other experimental hosts
