# FREE_GPU_STRATEGY: Complete Analysis & Setup Guide

## TL;DR: Best Free GPU Hosts for A1111 (2025)

**Recommended Daily Workflow:**
- **Primary:** AWS SageMaker Studio Lab (~4 hrs/day, 10 min setup)
- **Backup:** Google Colab (~1-2 hrs/day, 5 min setup)
- **Local Fallback:** CPU mode for dev/testing (unlimited, slow)

---

## Provider Comparison Matrix

| Provider | Daily GPU | Setup Time | Stability | Best For | Credit Card |
|----------|-----------|-----------|-----------|----------|-------------|
| **AWS SageMaker Studio Lab** | 4 hours | 10 min | 9/10 | Main dev env | NO |
| **Google Colab** | 1-2 hours | 5 min | 7/10 | Backup/testing | NO |
| **RunPod Free Trial** | 10-50 hrs | 5 min | 6/10 | Burst testing | YES (verify free) |
| **Lambda Labs** | 1 hour | 10 min | 8/10 | Heavy inference | YES (trial) |
| **Local CPU** | Unlimited | 2 min | 10/10 | Dev only | N/A |

---

## 1. AWS SageMaker Studio Lab (RECOMMENDED)

### Why It's Best
- **4 hours daily GPU** – enough for meaningful work
- **No credit card required** for free tier
- **T4 GPU** – decent performance for SDXL/ControlNet
- **Jupyter notebooks** – integrates perfectly with A1111 setup
- **Fast to start** – pre-installed Python, conda, git

### Setup (5 minutes)

1. Go to https://studiolab.sagemaker.aws/
2. Click "Sign in to get started"
3. Create free AWS account (NO card needed for Studio Lab tier)
4. In notebook:
   ```bash
   !git clone https://github.com/<your-fork>/stable-diffusion-webui.git
   %cd stable-diffusion-webui
   !bash dev/scripts/setup_sagemaker.sh
   ```
5. Wait ~3 minutes for deps
6. Start WebUI:
   ```bash
   !python launch.py --listen --xformers --opt-split-attention
   ```

### Daily Limits & Gotchas

| Limit | Value | Notes |
|-------|-------|-------|
| GPU hours/day | 4 | Resets at midnight UTC |
| CPU hours/day | 24 | Usually not bottleneck |
| Idle timeout | 1 hour | Auto-stops if no activity |
| Max session | 12 hours | Even if within quota |

**Key gotchas:**
- **Idle timeout stops GPU:** Run a dummy task every 30 min to keep alive
- **4-hour hard limit:** Timer resets at UTC midnight; restart notebook for fresh quota
- **First setup slow:** Initial pip install can take 2-3 min; subsequent runs are faster

### Workaround: Stay Alive Script

```python
import time
from datetime import datetime

def keep_alive():
    """Prevent idle timeout by running task every 30 min."""
    while True:
        print(f"[{datetime.now()}] Still working...")
        _ = [x**2 for x in range(1000000)]  # Busy work
        time.sleep(1800)  # 30 min

# Run in background cell with ! prefix
!nohup python -c "$(cat <<'EOF'
import time
from datetime import datetime
while True:
    print(f\"[{datetime.now()}] Still alive\")
    time.sleep(1800)
EOF
)" > /tmp/keepalive.log 2>&1 &
```

---

## 2. Google Colab (BACKUP)

### Why Use It
- **Easy setup** – just open notebook in Colab, run cells
- **1-2 hours GPU/day** – varies by region & time
- **Free tier** – no card needed
- **Built-in tunneling** – Colab provides CloudFlare tunnel automatically

### Setup (2 minutes)

1. Open `notebooks/a1111_colab.ipynb` on GitHub
2. Click "Open in Colab"
3. Run cells 1–4 sequentially
4. Wait ~5 min for A1111 install
5. Get public URL from cell output

### Daily Limits

| Limit | Value | Notes |
|-------|-------|-------|
| GPU hours/day | 1-2 | Highly variable |
| Max session | 12 hours | Idle timeout ~30 min |
| Restart limit | ~5/day | Hard cap per user |
| Premium option | $12.99/mo | Skip if free |

**Key gotchas:**
- **GPU availability sporadic:** Sometimes unavailable; try again 2 hrs later
- **Idle stops GPU quickly:** ~30 min of inactivity triggers reset
- **Tunnel link expires:** Reconnect if WebUI doesn't respond
- **Session restart needed:** After 12 hrs, must restart runtime

---

## 3. Other Options (Secondary)

### RunPod Free Trial (Experimental)
- **Setup:** 5 min, account required
- **GPU:** L40 for free trials, highly variable hours
- **Catch:** May require credit card for "verification" (funds not charged)
- **Use case:** Burst testing if SageMaker quota exhausted

### Lambda Labs (Not recommended for free)
- **1 hour free tier**, then $0.51/hour
- **Setup:** Complex; requires cloud knowledge
- **Use only if:** Budget available for occasional GPU

### Local CPU (Dev/Debug Only)
- **Setup:** `pip install` + `python launch.py`
- **Speed:** 30-60 sec per image (VERY slow)
- **Best for:** Testing API, LoRA loading, ControlNet configs
- **Avoid:** Actual image generation testing

---

## Recommended Daily Workflow (Maximizing 10+ min)

### Morning (SageMaker Priority)
```bash
1. Start SageMaker notebook
2. Run: python launch.py --listen --xformers
3. Expose port via ngrok
4. Test SDXL generation (2-3 prompts)
5. Test ControlNet (1 prompt)
6. Test face restoration (CodeFormer)
7. Load LoRA and test (1-2 prompts)
8. Shutdown (frees quota for next session)
# Total: ~15-20 min
```

### If SageMaker quota depleted: Fall back to Colab
```bash
1. Open Colab notebook
2. Repeat steps 3-7 above
3. Shutdown
# Total: ~10-15 min
```

### Local CPU (always available for API testing)
```bash
1. Run: python launch.py
2. Test API routes (no image generation)
3. Verify LoRA loading works
4. Test ControlNet endpoint exists
# Total: ~5 min, unlimited
```

---

## Cost Summary (Annual)

| Scenario | Cost | Limitation |
|----------|------|-------------|
| **SageMaker only** | $0 | 4 hrs/day GPU |
| **SageMaker + Colab** | $0 | 5-6 hrs/day GPU |
| **SageMaker + RunPod burst** | $0-10/mo | Variable |
| **Full unlimited** | ~$200-400/mo | Paperspace/Lambda paid |

**Recommendation:** Stick with SageMaker + Colab rotation. Covers 10+ min/day easily with $0 cost.

---

## Troubleshooting Free GPU Issues

### "Quota exhausted" but haven't used GPU?
- **Solution:** Log into AWS/Colab dashboard → check actual usage
- **Often:** Background kernel running from previous session
- **Fix:** Restart/reset notebook completely

### WebUI slow, even on GPU?
- **Cause:** SageMaker T4 is entry-level
- **Fix:** Reduce batch size, use `--lowvram` flag
- **Expected:** SDXL 512x512 = ~8-12 sec per image

### Can't expose public URL?
- **SageMaker:** Use ngrok (setup in `DEV_NOTES.md`)
- **Colab:** Already has CloudFlare tunnel; get link from cell output
- **Verify:** `curl http://<public-url>/api/sd-models` should return JSON

### GPU randomly stops mid-generation?
- **SageMaker:** Hit 4-hour daily limit → restart tomorrow
- **Colab:** Hit idle timeout → restart kernel
- **Workaround:** Implement keep-alive task (see SageMaker section)

---

## Next Steps

1. **Start with SageMaker:** Most reliable daily GPU
2. **Set up backup Colab:** For when SageMaker quota depleted
3. **Local CPU ready:** For API testing anytime
4. **See `dev/DEV_NOTES.md`:** Per-host detailed setup
5. **See `dev/hosts/*.md`:** Host-specific gotchas & fixes

---

## References

- [AWS SageMaker Studio Lab](https://studiolab.sagemaker.aws/)
- [Google Colab](https://colab.research.google.com/)
- [A1111 GitHub](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
- [Your Fork](https://github.com/tabrezahmed51/stable-diffusion-webui)
