# Video Upscaling Research: Cropped 4K Action Camera Footage

Research for the poomsae filming project. We're shooting 4K on 8× DJI Osmo Action 6 cameras at wide FOV, then cropping in post to isolate the athlete. This document analyses the resolution implications, compares upscaling tools, and recommends a workflow.

---

## 1. The Problem: Wide-Angle Crop Penalty

The DJI OA6 in Wide mode has a FOV ratio of 2.50 (from the studio planner app), meaning at any distance *d*, the visible width and height are each `2.50 × d` metres (1:1 sensor). In 16:9 mode (3840×2160), the vertical extent is reduced to `1.406 × d` metres.

The athlete is 1.70m tall; with high kicks, the bounding box is ~1.85m. Here's what that looks like in pixels across the range of distances she'll be from any given camera:

### Athlete pixel size in the raw 4K frame (3840×2160, 16:9 Wide)

| Distance | Visible height | Athlete % of frame (V) | Athlete height in px | Athlete width in px (est.) |
|----------|---------------|------------------------|---------------------|---------------------------|
| **2.5m** (closest approach) | 3.52m | 52.6% | **~1,136 px** | ~307 px |
| **3.5m** | 4.92m | 37.6% | **~812 px** | ~219 px |
| **4.5m** (centre line) | 6.33m | 29.2% | **~631 px** | ~170 px |
| **5.5m** | 7.73m | 23.9% | **~517 px** | ~139 px |
| **6.5m** (farthest) | 9.14m | 20.2% | **~437 px** | ~118 px |

> **Key insight**: Even in 4K, the athlete is only ~630 pixels tall at the centre-line distance of 4.5m, and ~437 pixels tall at maximum range. That's well below HD.

### What the crop looks like

If we crop the 16:9 frame so the athlete fills ~80% of the frame height (with some headroom and foot room), then maintain 16:9 aspect ratio:

| Distance | Crop window (px) | Effective resolution | Crop factor |
|----------|-----------------|---------------------|-------------|
| **2.5m** | 2,524 × 1,420 | **Above 1080p** | 1.52× |
| **3.5m** | 1,804 × 1,015 | **~1015p** | 2.13× |
| **4.5m** | 1,402 × 789 | **~789p** | 2.74× |
| **5.5m** | 1,148 × 646 | **~646p** | 3.34× |
| **6.5m** | 971 × 546 | **~546p** | 3.96× |

### Upscaling factor needed

| Distance | → 1080p (1920×1080) | → 1440p (2560×1440) | → 4K (3840×2160) |
|----------|---------------------|---------------------|-------------------|
| **2.5m** | None | 1.01× | 1.52× |
| **3.5m** | 1.06× | 1.42× | 2.13× |
| **4.5m** | 1.37× | 1.83× | 2.74× |
| **5.5m** | 1.67× | 2.23× | 3.34× |
| **6.5m** | 1.98× | 2.64× | 3.96× |

---

## 2. What Resolution Do We Actually Need?

The footage will be viewed in an app on iPhone and iPad screens. No current Apple device has a true 4K display:

| Device | Display resolution (landscape) | Effective "p" |
|--------|-------------------------------|---------------|
| iPhone 16 | 2556 × 1179 | ~1179p |
| iPhone 16 Pro Max | 2868 × 1320 | ~1320p |
| iPad Pro 11" (M5) | 2420 × 1668 | ~1668p |
| iPad Pro 13" (M5) | 2732 × 2048 | ~2048p |
| iPad Air 11" | 2360 × 1640 | ~1640p |

**Practical targets:**
- **1080p** — looks sharp on all iPhones (downscaled from ~1200p native). Acceptable on iPads.
- **1440p** — covers all iPhones perfectly and looks good on iPads (only the 13" iPad Pro could benefit from slightly more).
- **4K** — overkill for in-app viewing, but useful if users AirPlay to a TV.

**Recommendation: Target 1440p (2560×1440) as the delivery resolution.** This covers all iOS devices well while keeping file sizes manageable. The upscaling requirement is moderate: 1.0–2.6× across the distance range.

---

## 3. Can We Avoid the Problem? (FOV / Zoom Considerations)

### 2× Digital Zoom on the DJI OA6

The OA6 offers only two FOV steps: 1× Wide (fovRatio 2.50) and 2× Zoom (fovRatio 1.25). There's no intermediate option.

At 2× Zoom, the visible width at distance *d* is `1.25 × d`:
- At 4.5m → 5.63m visible width (mat is 9m — athlete could move outside frame in larger poomsae)
- At 6.5m → 8.13m visible width (barely covers the mat)

**Verdict: 2× is too narrow for most poomsae.** The athlete could step out of frame on the larger patterns. We must shoot at 1× Wide and crop in post.

### 4K Custom Mode (3840×3840, 1:1)

The DJI OA6's Custom mode records at the sensor's native 1:1 aspect (3840×3840 at up to 60fps). This doesn't change the pixel density (the athlete is the same number of pixels tall), but it provides:

1. **More vertical headroom** — captures 2.50×d metres vertically instead of 1.406×d. High kicks and low stances won't be clipped.
2. **More framing flexibility** — you can position the 16:9 crop window anywhere vertically in post, tracking her up/down movement.
3. **Same storage cost** — roughly same bitrate since DJI adjusts encoding.

**Recommendation: Shoot in 4K Custom mode (3840×3840).** The extra vertical data is free insurance and gives more room for dynamic cropping.

---

## 4. Tool Comparison: Topaz AI Alternatives

### Why not Topaz?

Topaz Video AI shifted to **subscription-only pricing** in October 2025:
- Personal: ~$299/year (~$25/month)
- Pro: ~$699/year (~$58/month)
- No lifetime/perpetual license available for new customers

For a project like this (a finite number of poomsae to process, not ongoing), paying $300+/year for a tool you'll use intensively for a few weeks is poor value.

### Paid alternatives

| Tool | Price | Quality (vs Topaz) | Speed (vs Topaz) | Best for | Notes |
|------|-------|--------------------|-------------------|----------|-------|
| **DaVinci Resolve Studio — Super Scale** | $295 one-time (perpetual) | 85–90% | 3–5× faster | Integrated workflow | Already needed for editing/keying. Super Scale is built into the timeline. Better temporal consistency — important for martial arts motion. Weaker on compression artifact removal. |
| **Aiarty Video Enhancer** | $165 one-time (3 PCs) | 90–95% | 2–3× faster | Standalone upscaling | Best value standalone upscaler. moDetail-HQ v2 model handles fine textures (hair, skin, fabric) well. Occasional halo artifacts on high-contrast edges. |
| **VideoProc Converter AI** | $46–58 one-time | ~80% | Fast | Budget option | Older AI models. Decent for mild upscales (1.5–2×), struggles with 3×+. |

### Free / open-source alternatives

| Tool | Quality (vs Topaz) | Ease of use | Speed | Notes |
|------|---------------------|-------------|-------|-------|
| **Video2X + Real-ESRGAN** | 80–85% | CLI (technical) | Moderate | Best free option. Processes frame-by-frame, so no temporal consistency (may cause flicker on motion). Works via GPU (Vulkan). |
| **FlashVSR** | ~85% | CLI (technical) | Moderate | Newer. Uses spatio-temporal modelling — analyses neighbouring frames for better motion consistency. Less mature than Real-ESRGAN. |
| **Upscale-Enhance** | 80–85% | CLI / macOS app | Moderate | Built on Real-ESRGAN. Has a native macOS app that uses Apple Neural Engine (no GPU needed). |

### Detailed comparison: DaVinci Resolve Super Scale vs Topaz Video AI

Since you're already using DaVinci Resolve, this is the most relevant head-to-head:

| Aspect | DaVinci Resolve Super Scale | Topaz Video AI |
|--------|----------------------------|----------------|
| **Upscale quality** | Very good. Clean, natural results. Slight softness at 3×+ compared to Topaz. | Best-in-class. Starlight diffusion models excel at extreme restoration. |
| **ML approach** | **Conservative/preservative** — uses the DaVinci Neural Engine (deep neural networks + ML, requires Studio version + capable GPU) to intelligently interpolate. Retains and enhances existing detail but does not invent new detail. Think "smart upscaling that understands edges and textures." | **Generative** — Starlight/diffusion models actively hallucinate new detail based on training data. Can produce sharper results on degraded footage, but may fabricate incorrect detail. |
| **Temporal consistency** | Excellent. Motion looks smooth, no flicker. | Good but can hallucinate artefacts on fast motion (ghost limbs, shimmering halos). |
| **Compression artifact removal** | Weak — doesn't address banding/posterisation. | Strong — de-banding and de-posterising built in. |
| **Speed** | Much faster (3–5× for equivalent upscale). | Slow. 6+ hours for a 10-minute 4K render is common. |
| **Workflow** | Integrated — upscale directly on the timeline alongside colour grading, keying, and audio. | Standalone — requires export → process → re-import round-trip. |
| **Cost** | $295 one-time (already needed for green screen keying and multi-cam editing). | $299–699/year subscription. |
| **Martial arts / sports** | Better choice — temporal stability means kicks and fast movement look clean. | May introduce artefacts on fast spinning/kicking movements. |
| **Best source material** | Clean, well-lit footage where detail exists but needs scaling up. Our use case (clean 4K crops from controlled studio lighting) plays to its strengths. | Heavily degraded, noisy, or compressed footage where detail must be reconstructed/invented. |

---

## 5. Recommended Workflow

### Primary approach: DaVinci Resolve (no additional cost)

Since DaVinci Resolve Studio is already in the pipeline for multi-camera editing, green screen keying, and colour grading:

```
1. SHOOT    → 4K Custom mode (3840×3840), 60fps, Standard colour, f/2.0, 1× Wide FOV
                 ↓
2. IMPORT   → DaVinci Resolve Studio, multi-camera timeline
                 ↓
3. KEY      → Green screen removal on the Color page (already planned)
                 ↓
4. CROP     → Dynamic keyframed crop to follow athlete's movement
             → Frame her at ~80% of height with headroom/foot room
             → Use the 1:1 source for vertical positioning flexibility
                 ↓
5. UPSCALE  → Apply Super Scale 2× on the cropped/reframed timeline
             → Set to "Enhanced" quality mode
             → This transforms e.g. a 1402×789 crop into 2804×1578 — above our 1440p target
                 ↓
6. GRADE    → Final colour grade, contrast, and sharpening
                 ↓
7. EXPORT   → H.265 (HEVC), 2560×1440, 30fps or 60fps
             → Bitrate: 15–20 Mbps for high quality on iOS
             → Container: MP4 (universal iOS compatibility)
```

### Fallback: Aiarty Video Enhancer ($165) for problem shots

For shots where the athlete is at maximum distance (6.5m) and the Super Scale result isn't sharp enough:

```
1. Export the problematic cropped clip from Resolve at native crop resolution
2. Run through Aiarty with moDetail-HQ v2 model at 2× or 4× upscale
3. Re-import into Resolve for final grading and export
```

This hybrid approach gives you DaVinci's temporal consistency on 90% of shots, with Aiarty's superior detail recovery available for the hardest cases.

### What you probably don't need

- **Topaz Video AI** — the subscription cost isn't justified for a finite project, and its temporal artefacts on fast motion are a concern for martial arts footage.
- **Real-ESRGAN / Video2X** — frame-by-frame processing will cause visible flicker on poomsae footage with continuous motion. Temporal consistency matters more than peak sharpness here.
- **4K output** — no iOS device displays true 4K. The storage and processing overhead isn't worth it for in-app viewing.

---

## 6. Resolution Reality Check

Let's validate the workflow with concrete numbers for the most common scenario (athlete at centre-line, 4.5m from camera):

```
Source:           3840 × 3840 (4K Custom mode)
                       ↓
Crop to athlete:  1402 × 789  (16:9 window, athlete fills ~80% height)
                       ↓
Super Scale 2×:   2804 × 1578 (above 1440p target)
                       ↓
Export at 1440p:  2560 × 1440 (slight downsample = added sharpness via supersampling)
```

**Result**: Even at the median distance, we exceed our 1440p target after a 2× upscale. At closer distances (2.5–3.5m), the crop window is already at or above 1080p natively, so the Super Scale 2× produces near-4K intermediate resolution that downsamples beautifully to 1440p.

At the worst case (6.5m):
```
Crop:             971 × 546
Super Scale 2×:   1942 × 1092  (just above 1080p, below 1440p)
Super Scale 4×:   3884 × 2184  (above 4K — but 4× pushes quality)
```

At 6.5m with 2× Super Scale, we just barely clear 1080p. For these distant shots, options include:
1. Accept 1080p quality (still looks good on iPhones, acceptable on iPads)
2. Use Aiarty for a higher-quality 3–4× upscale on these specific clips
3. Crop less aggressively (let the athlete be smaller in frame) — trade framing for resolution

---

## 7. Summary of Recommendations

| Decision | Recommendation | Rationale |
|----------|---------------|-----------|
| **Shooting mode** | 4K Custom (3840×3840) | Extra vertical data for cropping flexibility, no downside |
| **Target delivery** | 1440p (2560×1440) HEVC | Covers all iPhones and iPads, good file size |
| **Primary upscaler** | DaVinci Resolve Super Scale | Already in workflow, fast, good temporal consistency for martial arts |
| **Fallback upscaler** | Aiarty Video Enhancer ($165) | For problem shots at max distance; best value standalone tool |
| **Skip** | Topaz Video AI | Subscription pricing, temporal artefacts on fast motion |
| **FOV mode** | 1× Wide (not 2× Zoom) | 2× too narrow for poomsae floor patterns |

### Cost comparison for this project

| Option | Cost | Notes |
|--------|------|-------|
| DaVinci Resolve Studio only | $0 additional (already needed) | Handles 90%+ of shots well |
| DaVinci + Aiarty fallback | $165 one-time | Maximum quality across all distances |
| Topaz Video AI subscription | $299–699/year | Overkill, and worse for fast motion |

---

## Sources

- [DJI Osmo Action 6 Specs](https://www.dji.com/osmo-action-6/specs)
- [DJI Osmo Action 6 Settings Guide (DroneXL)](https://dronexl.co/2025/12/02/dji-osmo-action-6-settings-how-to-best-video/)
- [DJI Osmo Action 6 Announcement (CineD)](https://www.cined.com/dji-osmo-action-6-announced-variable-aperture-1-1-1-inch-sensor-4k-120fps/)
- [Resolve Super Scale vs Topaz (Frank Glencairn)](https://frankglencairn.wordpress.com/2022/08/18/upscaling-shoot-out-resolve-superscale-vs-topaz-video-enhance-ai-is-it-worth-it/)
- [Topaz Video vs DaVinci Resolve (Aiarty)](https://www.aiarty.com/ai-video-enhancer/topaz-video-vs-davinci-resolve.htm)
- [Topaz Video AI vs DaVinci Resolve Neural Engine (Alibaba Insights)](https://www.alibaba.com/product-insights/ai-video-upscalers-like-topaz-video-ai-vs-davinci-resolve-s-neural-engine-is-4k-restoration-worth-the-6-hour-render-time.html)
- [Aiarty Video Enhancer Review (Carl Travels)](https://www.carltravels.com/aiarty-video-enhancer-review.html)
- [Aiarty Video Enhancer Review (SLR Lounge)](https://www.slrlounge.com/aiarty-video-enhancer-review-enhance-restore-videos-in-4k-with-ai-free-license-giveaway/)
- [Topaz Labs Subscription Change (CG Channel)](https://www.cgchannel.com/2025/09/topaz-labs-to-end-perpetual-licenses-of-its-software/)
- [Topaz Video AI 2026 Review (VideProc)](https://www.videoproc.com/resource/topaz-video-ai-review.htm)
- [Open Source AI Video Upscalers (Aiarty)](https://www.aiarty.com/ai-video-enhancer/open-source-video-upscaler-enhancer.htm)
- [Video2X (GitHub)](https://github.com/k4yt3x/video2x)
- [iPhone 16 Pro Specs (Apple)](https://support.apple.com/en-us/121031)
- [iPad Pro Specs (Apple)](https://www.apple.com/ipad-pro/specs/)
- [iOS Resolution Reference](https://www.ios-resolution.com/)
