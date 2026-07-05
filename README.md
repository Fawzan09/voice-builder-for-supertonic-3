# 🎙️ Custom Voice Style Trainer for Supertonic 3 (Community Edition)

> **Unofficial, independent project.** Not affiliated with, endorsed by, or connected to Supertone Inc. "Supertonic" is referenced only to describe compatibility with Supertone's open-weight Supertonic 3 model. This is a community tool, not Supertone's official Voice Builder product.

A ready-to-run Google Colab notebook that trains a **custom voice style** for [Supertonic 3](https://github.com/supertone-inc/supertonic) from a short reference recording — no coding experience required, no local GPU needed.

---

## What this does

1. Takes a recording of a voice (yours, or one you have explicit permission to clone)
2. Extracts a speaker-identity embedding using the real optimizer from the open-source `supertonic3-voice-clone` project
3. Trains a custom voice style (`.json`) that plugs directly into Supertonic 3
4. Lets you preview and download the finished voice style, ready to use anywhere Supertonic 3 runs — offline, on-device, no ongoing API costs

**Runtime required:** Google Colab, GPU runtime (Runtime → Change runtime type → T4 GPU). A free Colab account is enough; Colab Pro is optional but speeds up training.

---

## Pipeline overview

| Step | What happens |
|---|---|
| 1 | Check GPU & clone the training repo |
| 2 | Install dependencies, download Supertonic 3 model assets |
| 3 | Upload your reference audio |
| 4 | Configure training settings |
| 5 | Preprocess audio (convert to 16kHz mono, merge, normalize) |
| 6 | Train the voice style |
| 7 | Test synthesis with your new voice |
| 8 | Download the trained `.json` voice style |

---

## Getting good results

**The single biggest factor is your reference recording, not any setting in the notebook.**

- Quiet room, phone mic 6-12 inches from your mouth
- 1-3 minutes of natural speech — varied pitch and sentence length, not a flat monotone read
- Single speaker, no background music or overlapping voices
- If your source audio is noisy, clean it up *before* uploading — no amount of downstream processing recovers detail that isn't in the original

### Why Step 5 (16kHz conversion) is required, and why it sounds different

Step 5 converts your audio to 16kHz mono before training. This is **not optional** — the speaker-identity model this pipeline uses (SpeechBrain's ECAPA-TDNN) was trained on 16kHz audio and requires input at that rate to work correctly.

If you compare the Step 5 preview clip to your original studio-quality file, it *will* sound duller — downsampling to 16kHz permanently removes frequency content above 8kHz. This is expected and does not affect your final output: Supertonic 3 generates final speech at full 44.1kHz 16-bit quality regardless of what the 16kHz reference file sounds like. The 16kHz file is only used to identify *who you sound like*, never played back to end users.

### Recommended training settings

| Parameter | Recommended | Notes |
|---|---|---|
| `NUM_STEPS` | 2500–3000 | Loss typically plateaus well before 5000; going higher mostly burns GPU time |
| `LEARNING_RATE` | 0.0001 | Stable default; lower if the loss curve looks jagged, raise slightly if it isn't moving |
| `REFERENCE_STYLE` | `"auto"` | Auto-selects the closest-matching stock voice as a starting point; try 1-2 alternates manually if the result's rhythm doesn't feel natural |
| `EARLY_STOP_LOSS_THRESHOLD` | 0.02–0.03 | Stops training once gains flatten out |
| `VOCODER_STEPS` | 10–15 | Controls fidelity during training preview |
| `QUALITY_STEPS` (final synthesis) | up to **12** | This is Supertonic's own documented maximum for this parameter (range: 5 low – 12 high, default 8). Values above 12 are outside the tested range and provide no documented benefit. |

### A known limitation, honestly stated

This method trains only the *tonal/identity* part of the voice style. The rhythm and pacing (prosody) are inherited from whichever stock voice is used as the starting point and are not trained on your specific speech patterns. Combined with the fact that Supertone's original proprietary speaker encoder was never publicly released — this pipeline uses an open substitute instead — expect a close, recognizable likeness rather than a studio-perfect indistinguishable clone. That's an honest ceiling of this open-source approach, not a bug or a missing setting.

---

## Responsible use — required, not optional

The underlying Supertonic 3 model is released under the **OpenRAIL-M license**, which carries binding "Responsible AI" use restrictions. By using this notebook you agree to:

- **Consent** — only clone your own voice, or a voice you have explicit, verifiable permission to clone
- **No deception** — never use a cloned voice to impersonate, defraud, or deceive someone
- **No protected-category harm** — do not target or impersonate minors, or create without-consent depictions of any real person
- **Disclosure** — if you share audio generated with this tool publicly, disclose that it is AI-generated

These terms are a condition of the underlying model's license, not a suggestion, and apply to anyone using this notebook.

---

## License & attribution

The training code is adapted from the open-source `supertonic3-voice-clone` project (MIT License), via a maintained fork. See the `NOTICE` file in the source repository for the full copyright notice. Supertonic 3 itself is released by Supertone Inc. under its own model license (OpenRAIL-M) — see [supertone-inc/supertonic](https://github.com/supertone-inc/supertonic) for details.

This product is sold as a packaged, documented, ready-to-run notebook. It does not grant any rights to the underlying open-source code or model beyond what their original licenses already permit.

---

## Included example voices

An `audio/` folder is included with the notebook, containing pre-trained voice styles plus sample clips in multiple languages so you can hear results before training your own:

```
audio/
├── men/
│   ├── source.mpeg      # original reference recording used for training
│   ├── output_en.wav    # English sample
│   ├── output_hi.wav    # Hindi sample
│   ├── output_ar.wav    # Arabic sample
│   ├── output_jp.wav    # Japanese sample
│   └── output_ko.wav    # Korean sample
├── women/
│   ├── source.mp3       # original reference recording used for training
│   ├── output_en.wav    # English sample
│   ├── output_hi.wav    # Hindi sample
│   ├── output_ar.wav    # Arabic sample
│   ├── output_jp.wav    # Japanese sample
│   └── output_ko.wav    # Korean sample
└── jarvis/
    └── output_en.wav    # English sample — no source/reference recording
```

### 🎧 Listen to the samples

**Men**

| Language | Source | Output |
|---|---|---|
| Original reference | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/source.mpeg) | — |
| English | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/output_en.wav) |
| Hindi | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/output_hi.wav) |
| Arabic | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/output_ar.wav) |
| Japanese | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/output_jp.wav) |
| Korean | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/men/output_ko.wav) |

**Women**

| Language | Source | Output |
|---|---|---|
| Original reference | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/source.mp3) | — |
| English | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/output_en.wav) |
| Hindi | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/output_hi.wav) |
| Arabic | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/output_ar.wav) |
| Japanese | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/output_jp.wav) |
| Korean | — | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/women/output_ko.wav) |

**Jarvis**

| Language | Source | Output |
|---|---|---|
| English | — none | [▶ Play](https://github.com/Fawzan09/voice-builder-for-supertonic-3/raw/main/audio/jarvis/output_en.wav) |

> Playback works when this README is viewed on GitHub. If you're reading this elsewhere (e.g. as a plain text file), the links above can be opened directly in a browser to play the clips.

To use any of these voices directly without training your own: point Supertonic 3's synthesis step at the corresponding trained `.json` voice style instead of running Steps 3–6.

## Get this notebook

Available here: **[buymeacoffee.com/fawzan/e/554254](https://buymeacoffee.com/fawzan/e/554254)**

**One-time purchase — unlimited voice cloning.** Pay once, keep the notebook forever. Train as many voice styles as you want, whenever you want — no subscriptions, no per-voice fees, no usage limits. Everything runs on your own free Google Colab GPU, so there are no ongoing costs from us at all.

## Support

This is a small, independently maintained tool. If something breaks or a step doesn't run as expected, double check:
- GPU runtime is enabled (Runtime → Change runtime type → T4 GPU)
- You're running cells in order, top to bottom
- Your reference audio uploaded correctly in Step 3

If you're still stuck, message me directly through the [purchase page](https://buymeacoffee.com/fawzan/e/554254).

---

## Disclaimer

This tool is provided as-is, with no warranty of any kind. You are solely responsible for ensuring you have the right to use any voice you train with this tool, and for how you use the resulting output.
