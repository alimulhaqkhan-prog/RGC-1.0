<div align="center">

```
██████╗   ██████╗    ██████╗
██╔══██╗ ██╔════╝   ██╔════╝
██████╔╝ ██║  ███╗  ██║
██╔══██╗ ██║   ██║  ██║
██║  ██║ ╚██████╔╝  ╚██████╗
╚═╝  ╚═╝  ╚═════╝    ╚═════╝
```

# Resonance Gap Curiosity — RGC

### *A Source-Aware Ask–Verify–Update Curiosity Architecture*
### *for Memory-Bearing AI Systems*

<br>

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19976745.svg)](https://doi.org/10.5281/zenodo.19976745)
![Version](https://img.shields.io/badge/version-v1.4b-blue)
![Stage](https://img.shields.io/badge/stage-research--prototype-orange)
![Validation](https://img.shields.io/badge/validation-17%20controlled%20tests-brightgreen)
![Published](https://img.shields.io/badge/published-May%202026-lightgrey)
![License](https://img.shields.io/badge/rights-all%20rights%20reserved-red)
![Patent](https://img.shields.io/badge/patent-GB2518804.6-purple)

<br>

[📄 Zenodo Archive](https://doi.org/10.5281/zenodo.19976745) · [🧠 Core Equations](#core-equations) · [📊 Validation Results](#validation-summary) · [⚠️ Non-Claims](#non-claims) · [📖 Citation](#citation)

</div>

---

## One-Line Summary

RGC is a source-aware **Ask–Verify–Update** curiosity architecture for memory-bearing AI systems — designed to classify *novelty*, *conflict*, *ambiguity*, and *reward re-engagement* before any confident generation or local memory update.

---

## 🧠 What Is This?

Most AI curiosity systems ask:

> **"What is unknown?"**

RGC asks a second, safety-critical question:

> **"What is *familiar* but now *contradictory* — and should memory be revised?"**

This distinction matters because the most dangerous memory update is not always the unfamiliar input.  
Sometimes it is a *familiar* input carrying contradictory, unreliable, or weakly sourced information —  
and novelty-only systems are not typically designed to treat this as a separate risk signal.

RGC formalises this condition as the **Conflict Curiosity Blindspot** and routes the system toward:
- Identification (novelty)
- Clarification (ambiguity)
- Reconciliation (familiar contradiction)
- Engagement (reward re-engagement)
- Source verification (CSLV-compatible)
- Bounded local memory update

---

## 🚀 Why This Matters

| Problem | Existing Approach | RGC Approach |
|---|---|---|
| Unknown input | Novelty curiosity (ICM, RND) | Cn → Identification |
| Familiar contradiction | Usually not explicit | Cc → Reconciliation |
| Partial recognition | Usually not separated | Ca → Clarification |
| Memory overwrite safety | Usually not update-gated | CSLV-weighted update |
| Poisoning resistance | Usually not modelled | Contradiction penalty |

The **Cc Blindspot** is the core finding: recognition-only systems classify a familiar-but-contradictory input as *KNOWN* with no verification trigger, while RGC classifies it as *RECONCILIATION / Conflict*.

---

## Core Equations

### Recognition Resonance R⁺

```
R⁺_sim = max_i [ sim⁺(TM, BM_i) · intensity_i · weight_factor_i ]
sim⁺(a,b) = clip( (cosine_sim(a, b) + 1) / 2 , 0, 1 )
```

### Four Curiosity Components

```
Cn(t) = 1 − R⁺                                              # Novelty
Cc(t) = R⁺ · |V_TM(t) − V_BM| / V_max                      # Conflict  ← central
Ca(t) = 4 · R⁺ · (1 − R⁺) · I(R⁺ ∈ [θ_lo, θ_hi])          # Ambiguity
Cr(t) = clip( W_old · max(V_BM, 0) , 0, 1 )                 # Reward
```

### Total Curiosity Score

```
C_score(t) = α·Cn + β·Cc + γ·Ca + δ·Cr    (α + β + γ + δ = 1)
```

### Source-Aware Memory Update (CSLV-Compatible)

```
CSLV_Conf          = S_val · I(S_val ≥ θ_val)
CSLV_Contradiction = max(0, 1 − C_att) · I(R_att ≥ θ_rel)

W_new = clip( W_old + η · C_score · CSLV_Conf − μ · CSLV_Contradiction , 0, 1 )
```

| Source Type | Effect |
|---|---|
| Verified source | W increases proportionally |
| Partial / below threshold | No confirmed increase; may slightly decrease if contradiction penalty applies. |
| Irrelevant source | No update (safe default) |
| Unreliable / poisoning attempt | W decreases (resistance) |

> Update applies **only** to the resonance-matched BM trace. All other memory traces remain unchanged.

---

## Ask–Verify–Update Flow

```
 Current input / TM state
         │
         ▼
 TM–BM resonance  →  R⁺ computed
         │
         ▼
 ┌───────────────────────────────┐
 │   RGC Gap Classification      │
 │                               │
 │  R⁺ low      →  Cn  →  Identify      │
 │  R⁺ mid      →  Ca  →  Clarify       │
 │  R⁺ high +   →  Cc  →  Reconcile     │
 │   valence gap                  │
 │  R⁺ high +   →  Cr  →  Engage        │
 │   positive V                   │
 └───────────────────────────────┘
         │
         ▼
  Ask / Clarify / Reconcile / Verify
         │
         ▼
  CSLV-compatible source check
  (Conf / Contradiction)
         │
         ▼
  Bounded local BM update
  W_new = clip( W_old + η·C_score·CSLV_Conf − μ·CSLV_Contradiction , 0, 1 )
```

---

## Validation Summary

> **These are controlled mechanistic simulations, not deployment benchmarks.**  
> All tests use sentence-transformers/all-MiniLM-L6-v2 · CPU · Google Colab · No fine-tuning.

| Package | Purpose | Tests | Result |
|---|---|---|---|
| **v1.2** | Core RGC validation | 5 | ✅ 5/5 PASS |
| **v1.3** | Advanced stress tests | 7 | ✅ 7/7 PASS |
| **v1.4b** | Focused Ca + CSLV validation | 5 | ✅ 5/5 PASS |
| **Total** | Controlled mechanistic tests | **17** | **✅ 17 PASS** |

---

### v1.2 — Core Validation

| Test | Description | Result |
|---|---|---|
| P1 | Novelty → ask before generating | ✅ PASS |
| P2 | Few-shot BM consolidation (R⁺=0.448, Best=trace_1) | ✅ PASS |
| P3 | Conflict Curiosity (Cc=0.342, Mode=RECONCILIATION / Conflict) | ✅ PASS |
| P4 | Local BM update isolation | ✅ PASS |
| P5 | Habituation + reactivation (Reactivation Cc=0.325) | ✅ PASS |

---

### v1.3 — Advanced Stress Tests

| Test | Description | Result |
|---|---|---|
| A1 | Multi-novelty detection | ✅ PASS |
| A2 | Paraphrase recognition (mean R⁺=0.690) | ✅ PASS |
| A3 | Conflict Curiosity gradient (Neutral Cc=0.192 → Conflict Cc=0.416) | ✅ PASS |
| A4 | **R-only ablation — Cc Blindspot** | ✅ PASS |
| A5 | Local update isolation | ✅ PASS |
| A6 | Poisoning guard (ΔW: +0.0576, +0.0288, −0.0500) | ✅ PASS |
| A7 | Habituation + conflict reactivation (Reactivation Cc=0.325) | ✅ PASS |

**Strongest result — A4 R-only ablation:**

```
R-only monitor  →  KNOWN / NO ASK        ← Blindspot
RGC             →  RECONCILIATION / Conflict  ← Correct
```

---

### v1.4b — Focused Ca + CSLV Validation

| Test | Observed Behaviour | Result |
|---|---|---|
| T1 | Ca forward: exact Ca=0, partial Ca=0.855–0.958, novel Ca=0 | ✅ PASS |
| T2 | Recognition guard: familiar+contra → Reconciliation; novel+danger → Identification | ✅ PASS |
| T3 | CSLV gating: verified ΔW=+0.075; irrelevant ΔW=0.000; poisoning ΔW=−0.080 | ✅ PASS |
| T4 | Local isolation: 4 BM traces — only matched trace updated | ✅ PASS |
| T5 | R-only blindspot + CSLV response: CSLV_Conf=0.90 → ΔW=+0.083 | ✅ PASS |

**v1.4b T1 Ca validation detail:**

| Input Type | N | R⁺ Range | Ca Range | Mode |
|---|---|---|---|---|
| Exact / familiar | 2 | 0.759–0.925 | 0.000 | Standard |
| Partial / ambiguous | 4 | 0.603–0.690 | 0.855–0.958 | Clarification |
| Novel / unrelated | 3 | 0.029–0.110 | 0.000 | Identification |

---

## Repository Structure

```
RGC-1.0/
│
├── README.md
├── CITATION.bib
├── requirements.txt
│
├── paper/
│   ├── RGC_v1_4b_Preprint.pdf
│   ├── RGC_v1_4b_Preprint.docx
│   └── RGC_v1_4b_Journal.docx
│
├── notebooks/
│   ├── RGC_v1_2_Core_Validation.ipynb
│   ├── RGC_v1_3_Advanced_StressTests.ipynb
│   └── RGC_v1_4b_Focused_Ca_CSLV_Validation.ipynb
│
├── results/
│   ├── RGC_v1_4b_T1_strong_Ca_forward_validation.csv
│   ├── RGC_v1_4b_T2_recognition_guarded_Cc.csv
│   ├── RGC_v1_4b_T3_CSLV_update_gating.csv
│   ├── RGC_v1_4b_T4_local_update_isolation.csv
│   ├── RGC_v1_4b_T5_R_only_blindspot_CSLV_response.csv
│   └── figures/
│       └── RGC_v1_4b_Ca_forward_validation.png
│
└── companion/
    └── related_frameworks.md
```

---

## How to Run

### Option 1 — Google Colab (Recommended)

1. Open [Google Colab](https://colab.research.google.com/)
2. Upload a notebook from `notebooks/`
3. Click **Runtime → Run all**
4. No GPU required · No API key · Free tier sufficient

### Option 2 — Local Environment

```bash
pip install -r requirements.txt
jupyter notebook notebooks/
```

### Requirements

```
sentence-transformers>=2.2.0
numpy
pandas
matplotlib
jupyter
```

**Embedding model:** `sentence-transformers/all-MiniLM-L6-v2` (auto-downloaded)

---

## Related Frameworks

RGC is part of the **AURA-X Ω** continuity-first research direction:

| Framework | Role | Link |
|---|---|---|
| **AURA** | TM–BM resonance · bounded meaning-state foundation | [aura-x-omega-2025](https://github.com/alimulhaqkhan-prog/aura-x-omega-2025) |
| **ACI** | Runtime continuity-health monitoring · Silent Alarm | [alim-continuity-index](https://github.com/alimulhaqkhan-prog/alim-continuity-index) |
| **CSLV** | Source-aware verification interface | companion framework |
| **RGC** | Active curiosity · Ask–Verify–Update control | **this repo** |

> Other companion frameworks (ANIMA, RGCC-X+, CRM/AEC, Wajdan, UPS) are referenced as future extensions and are not claimed as validated components of the present RGC simulation package.

---

## Intended Application Areas

RGC is a research-stage architecture that may be relevant to:

- 🤖 Memory-bearing AI agents and persistent-context assistants  
- 🏢 Enterprise copilots with long-term user memory  
- 🔍 Retrieval-augmented generation (RAG) systems  
- 🛡️ AI safety monitoring and hallucination-risk reduction  
- ⚖️ Legal, medical, and compliance decision-support assistants  
- 🚗 Robotics and autonomous decision-support systems  
- 🔬 AI trust, governance, and memory integrity research  

RGC is especially relevant where systems must **avoid blindly overwriting stored knowledge** and should instead ask, verify, reconcile, or update memory in a bounded and source-aware way.

---

## Non-Claims

RGC does **not** claim to be:

- ❌ A certified safety system
- ❌ A production-ready deployment
- ❌ A guarantee against hallucination
- ❌ A conscious, emotional, or sentient system
- ❌ A replacement for domain-specific safety validation
- ❌ A full CSLV L1–L4 pipeline implementation (v1.4b T3 tests CSLV-compatible gating only)

> The current validation package constitutes controlled mechanistic simulations. Broader validation across real agent logs, different embedding models, and end-to-end memory systems remains future work.

---

## Archive and DOI

This work is permanently archived on Zenodo:

**DOI:** [`10.5281/zenodo.19976745`](https://doi.org/10.5281/zenodo.19976745)

> GitHub is the living development space.  
> Zenodo is the archival record for reproducibility.  
> Scientific claims are based on the archived Zenodo versions.

---

## Citation

```bibtex
@misc{khan2026rgc,
  author       = {Khan, Alim ul Haq},
  title        = {Resonance Gap Curiosity (RGC): A Source-Aware Ask--Verify--Update
                  Curiosity Architecture for Memory-Bearing AI Systems},
  year         = {2026},
  publisher    = {Zenodo},
  version      = {v1.0},
  doi          = {10.5281/zenodo.19976745},
  url          = {https://doi.org/10.5281/zenodo.19976745},
  note         = {Research-stage architecture · v1.2, v1.3, and v1.4b controlled
                  MiniLM-embedding validation packages · 17 mechanistic tests.}
}
```

---

## Rights and Copyright

**Copyright © 2026 Alim ul Haq Khan. All rights reserved.**

This work is associated with **UK Patent Application No. GB2518804.6**.

No permission is granted for reproduction, redistribution, modification, adaptation, commercial use, AI training use, or derivative use without the author's prior written consent.

Academic citation and reference with proper attribution are permitted.

---

## Author and Contact

**Alim ul Haq Khan**  
Independent Researcher · Timergara, Khyber Pakhtunkhwa, Pakistan  
ORCID: [0009-0001-4708-0365](https://orcid.org/0009-0001-4708-0365)  
GitHub: [@alimulhaqkhan-prog](https://github.com/alimulhaqkhan-prog)

---

<div align="center">

**Research-stage architecture · Controlled mechanistic simulation package**  
**Not deployment-certified · Not safety-certified**  
**Preprint v1.0 archived on Zenodo**

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19976745.svg)](https://doi.org/10.5281/zenodo.19976745)

</div>
