<h1 align="center">EmoIdiomBench</h1>

<p align="center">
  <strong>A Bilingual Benchmark for Figurative Reasoning and Spatial Visual Parsing in MLLMs</strong>
</p>

<p align="center">
  <a href="https://github.com/Oliveeez/EmoIdiomBench-ACM-MM-2026/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg" alt="License"></a>
  <a href="README_zh.md"><img src="https://img.shields.io/badge/简体中文-Click-blue.svg" alt="中文"></a>
  <img src="https://img.shields.io/badge/Idioms-4%2C543-green.svg" alt="Idioms">
  <img src="https://img.shields.io/badge/Images-17%2C875-orange.svg" alt="Images">
  <img src="https://img.shields.io/badge/ACM%20MM%202026-Dataset%20Track-purple.svg" alt="ACM MM 2026">
</p>

---

## 📌 Overview

**EmoIdiomBench** is a large-scale bilingual (Chinese + English) multimodal benchmark that evaluates Multimodal Large Language Models (MLLMs) on two dimensions:

1. **Figurative Reasoning** — decoding culturally rooted idioms from rendered emoji images via semantic, homophonic, and metaphorical mappings.
2. **Spatial Visual Parsing** — interpreting emojis under non-linear spatial layouts (grid, diagonal, star, etc.) with or without explicit ordering cues.

The benchmark adopts a dual **Open-Ended Generation (OE) / Multiple-Choice (MC)** evaluation protocol, exposing a critical "generative aphasia" in current MLLMs: most models can passively verify visual-textual alignments under MC, yet fundamentally fail to actively construct cross-modal reasoning paths under OE.

<p align="center">
  <img src="images/teaser.png" alt="EmoIdiomBench Overview" width="95%"/>
</p>

## ✨ Highlights

- 🌏 **Bilingual**: 3,333 Chinese four-character idioms + 1,210 English idioms.
- 🖼️ **17,875 rendered images** using the open-source Twemoji set on 1024×1024 canvases.
- 🔬 **Controllable construction**: Chinese subtask uses a pinyin-indexed B⁺ tree with enumerable homophonic substitution ratios (88.59% of idioms require ≥1 homophonic mapping).
- 📐 **Spatial layout variants**: Base / Layout (non-linear) / Guided (with arrows or numerical labels) for the Chinese subtask.
- 📝 **Dual OE/MC protocol**: Systematically contrasts generative and discriminative abilities.
- 🏆 **12 MLLMs evaluated**: Including GPT-o3, GPT-5.4, Gemini-2.5-Pro, Claude-Sonnet-4.5, Qwen2.5-VL, InternVL3, and more.

## 📂 Repository Structure

```
EmoIdiomBench-ACM-MM-2026/
├── data/
│   ├── chinese_variants/                        # Chinese subtask images
│   │   ├── 1_一丘之貉/                           # One folder per idiom: {id}_{idiom}
│   │   │   └── 1/                               # Variant set index
│   │   │       ├── 一丘之貉_base_v001.png        # Base: standard left-to-right layout
│   │   │       ├── seq_varients_pure/            # Layout variants (no ordering cues)
│   │   │       │   ├── 一丘之貉_diagonal_v002.png
│   │   │       │   └── 一丘之貉_zigzag_v003.png
│   │   │       └── seq_varients_with_guideance/  # Guided variants (arrows / labels)
│   │   │           ├── 一丘之貉_diagonal_guide_only_v004.png
│   │   │           └── 一丘之貉_zigzag_numbers_only_v005.png
│   │   ├── 2_.../
│   │   └── ...
│   ├── english_variants/                        # English subtask images (Base only)
│   │   ├── 1_trade_off/
│   │   │   └── trade_off_base.png
│   │   ├── 2_give_me_a_break/
│   │   │   └── give_me_a_break_base.png
│   │   └── ...
│   └── raw_data/                                # Source annotation files (JSON)
│       ├── chinese_idioms.json
│       ├── english_idioms.json
│       ├── chinese_mc_options.json
│       └── english_mc_options.json
├── images/                                      # Figures used in this README
├── README.md                                    # This file (English)
├── README_zh.md                                 # 中文版 README
└── LICENSE
```

## 📊 Dataset Statistics

| | Chinese | English |
|---|---|---|
| Idioms | 3,333 | 1,210 |
| Emojis per instance | 4 (fixed) | 2–10 (avg. 2.79) |
| Mapping types | Semantic + Homophonic | Semantic + Metaphor |
| Homophonic rate | 88.59% | N/A |
| Image variants / idiom | 5 (Base ×1, Layout ×2, Guided ×2) | 1 (Base only) |
| Total images | 16,665 | 1,210 |
| **Total** | **4,543 idioms / 17,875 images** | |

## 🔧 Data Format

Each entry in the Chinese JSON annotation file follows this schema:

```json
// data/raw_data/chinese_idiom_with_distractors_index1.json
{
    "idiom_index": 1557,
    "idiom": "怒火中烧",
    "emoji_rep": [
        {
            "index": 1,
            "emoji_set": "💢🔥⏰🥄",
            "homophonic_num": 2
        }
    ],
    "distractors": [
        "心急如焚",
        "妒火中烧",
        "走马观花",
        "恼羞成怒"
    ]
}
```

| Field | Description |
| --- | --- |
| `idiom_index` | Unique integer ID for the idiom |
| `idiom` | Target four-character Chinese idiom |
| `emoji_rep` | List of emoji representation variants. Each contains an `index` (variant ID), `emoji_set` (the four rendered emojis), and `homophonic_num` (number of homophonic substitutions in this variant) |
| `distractors` | Four MC options (including the correct answer) for the multiple-choice setting |

The corresponding images are stored under `data/chinese_variants/{idiom_index}_{idiom}/{variant_index}/` with Base, Layout, and Guided sub-files as described in the repository structure above.

The English JSON annotation file follows a similar schema:

```json
{
    "idiom_index": 3,
    "idiom": "swan song",
    "emoji_rep": [
        {
            "emoji_set": "🦢🎵"
        }
    ],
    "distractors": [
        "call it a day",
        "for a song",
        "spill the beans",
        "throw in the towel"
    ]
}
```

The corresponding images are stored under `data/english_variants/{idiom_index}_{idiom}/` with a single Base image.

## 📈 Benchmark Results

### Main Results (Accuracy %)

| Model | CH OE-Base | CH OE-Layout | CH OE-Guided | CH MC | EN OE-Strict | EN OE-Match | EN MC |
|---|---|---|---|---|---|---|---|
| **GPT-o3** | **60.16** | **57.93** | **59.47** | **98.20** | **56.59** | **60.71** | **96.45** |
| Gemini-2.5-Pro | 33.17 | 29.13 | 31.74 | 95.04 | 46.15 | 52.03 | 94.87 |
| GPT-5.4 | 9.78 | 8.50 | 9.67 | 66.85 | 23.65 | 27.58 | 88.84 |
| Claude-Sonnet-4.5 | 9.27 | 8.04 | 8.16 | 61.75 | 23.13 | 27.49 | 81.07 |
| GPT-4o | 7.50 | 5.46 | 6.43 | 66.43 | 23.06 | 27.85 | 86.03 |
| Qwen2.5-VL (72B) | 6.81 | 6.05 | 5.87 | 66.10 | 7.92 | 10.60 | 81.85 |
| InternVL3 (38B) | 5.87 | 4.84 | 4.51 | 62.72 | 7.28 | 9.49 | 76.87 |
| MiniCPM-V 4.5 (8B) | 3.95 | 3.11 | 2.94 | 51.00 | 6.22 | 8.16 | 68.38 |
| Qwen3-VL (8B) | 3.88 | 3.53 | 3.79 | 42.35 | 1.88 | 2.54 | 47.24 |
| Qwen2.5-VL (7B) | 3.82 | 2.85 | 2.33 | 52.71 | 4.58 | 5.81 | 65.84 |
| InternVL3 (8B) | 2.26 | 1.21 | 1.48 | 49.03 | 4.01 | 5.48 | 67.41 |
| Llama-3.2-V (11B) | 1.49 | 0.92 | 0.76 | 32.28 | 3.47 | 4.63 | 40.53 |
| | | | | | | | |
| Human (Avg.) | 69.25 | – | – | 97.63 | 65.81 | 71.06 | 96.87 |
| Human (Best) | 80.12 | – | – | 99.74 | 78.25 | 83.95 | 98.21 |

<p align="center">
  <img src="images/radar.png" alt="Radar Chart" width="55%"/>
</p>

### Key Findings

1. **Generative Aphasia**: Massive OE↔MC gap across all models (e.g., Qwen2.5-VL-72B: 6.81% OE vs. 66.10% MC on Chinese).
2. **Phonetic Reasoning Bottleneck**: Chinese homophonic mappings cause severe failures — models cannot perform "visual → phonetic → linguistic" multi-hop reasoning.
3. **Spatial Parsing Deficiency**: Non-linear layouts degrade performance, and guided cues (arrows/labels) are sometimes treated as semantic noise rather than instructional aids.
4. **Human Gap**: Even GPT-o3 (60.16%) significantly trails human average (69.25%) and expert ceiling (80.12%) on Chinese OE-Base.


### Prompt Templates

We provide the exact prompt templates used in our evaluation in the paper. Models are instructed to output structured JSON with `inference_chain` and `predicted_idiom` (OE) or `predicted_answer` (MC). See **Section 4.1** of the paper for full implementation details.

## 📋 Evaluation Metrics

| Setting | Metric | Description |
|---|---|---|
| Chinese OE | Exact Match | Predicted idiom must exactly match the target |
| English OE | Strict Accuracy | Exact match after normalization |
| English OE | Match Accuracy | Character-level Jaccard similarity ≥ 0.85 |
| English OE | AvgMatch | Mean character-level Jaccard similarity |
| MC (both) | Accuracy | Correct option selected from 4 choices |



## 🙏 Acknowledgments

We thank all annotators who participated in the human curation and evaluation. We also thank the developers of [Twemoji](https://github.com/twitter/twemoji) for the open-source emoji assets, and [DeepSeek-R1](https://github.com/deepseek-ai/DeepSeek-R1) for powering parts of our construction pipeline.

## 📬 Contact

For questions or suggestions, please open an issue or contact:

- **Zijiao Zhang** — [zijiao.zhang@sjtu.edu.cn](mailto:zijiao.zhang@sjtu.edu.cn)
- **Renqiu Xia** (Corresponding) — [xiarenqiu@sjtu.edu.cn](mailto:xiarenqiu@sjtu.edu.cn)
- **Junchi Yan** (Corresponding) — [yanjunchi@sjtu.edu.cn](mailto:yanjunchi@sjtu.edu.cn)