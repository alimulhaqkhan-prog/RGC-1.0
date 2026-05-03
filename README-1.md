<div align="center">

<pre>
+--------------------------------------------------------------------+
|                                                                    |
|        RGC - RESONANCE GAP CURIOSITY                               |
|                                                                    |
|        Source-Aware Ask -> Verify -> Update                        |
|        Curiosity Architecture for Memory-Bearing AI Systems         |
|                                                                    |
+--------------------------------------------------------------------+

        TM input / current context
                    |
                    v
             +--------------+
             |  R+ resonance|
             +--------------+
                    |
                    v
      +--------------------------------+
      |  Gap Classification            |
      |                                |
      |  Cn : Novelty       -> Ask     |
      |  Ca : Ambiguity     -> Clarify |
      |  Cc : Conflict      -> Verify  |
      |  Cr : Re-engagement -> Engage  |
      +--------------------------------+
                    |
                    v
        Source check / CSLV-compatible gate
                    |
                    v
             Bounded local BM update
</pre>

# Resonance Gap Curiosity — RGC

### *A Source-Aware Ask–Verify–Update Curiosity Architecture*
### *for Memory-Bearing AI Systems*

<br>

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19976745.svg)](https://doi.org/10.5281/zenodo.19976745)
![Version](https://img.shields.io/badge/version-v1.4b-blue)
![Stage](https://img.shields.io/badge/stage-research--prototype-orange)
![Validation](https://img.shields.io/badge/validation-17%20controlled%20tests-brightgreen)
![Published](https://img.shields.io/badge/published-May%202026-lightgrey)
![Rights](https://img.shields.io/badge/rights-all%20rights%20reserved-red)
![Patent](https://img.shields.io/badge/patent-GB2518804.6-purple)

<br>

[🚀 Live Demo Prototype](https://alimulhaqkhan-prog.github.io/RGC-1.0/) ·
[📄 Zenodo Archive](https://doi.org/10.5281/zenodo.19976745) ·
[🧠 Core Equations](#core-equations) ·
[📊 Validation Results](#validation-summary) ·
[⚠️ Non-Claims](#non-claims) ·
[📖 Citation](#citation)

</div>

---

## One-Line Summary

**Resonance Gap Curiosity (RGC)** is a research-stage, source-aware **Ask–Verify–Update** architecture for memory-bearing AI systems. It classifies the gap between active context and stored memory into **novelty**, **ambiguity**, **conflict**, and **reward re-engagement** before confident generation or bounded local memory update.

---

## Live Demo Prototype

A lightweight browser prototype is included in this repository as:

```text
index.html
```

Open it locally by double-clicking `index.html`, or enable GitHub Pages and use:

```text
https://alimulhaqkhan-prog.github.io/RGC-1.0/
```

The demo is intentionally **research-stage**. It visualizes RGC behavior and the Ask–Verify–Update flow. It is not a production safety system, not an autonomous-agent controller, and not a substitute for domain validation.

---

## What Is RGC?

Most curiosity systems mainly ask:

> **What is unknown?**

RGC adds a second safety-critical question:

> **What is familiar but now contradictory — and should memory be revised?**

That distinction matters because the riskiest memory update is not always caused by unfamiliar input. Sometimes the dangerous case is a **familiar input** carrying contradictory, weakly sourced, or unreliable information. A novelty-only system may treat it as already known and fail to trigger verification.

RGC formalizes this condition as the **Conflict Curiosity Blindspot** and routes the system toward:

- **Identification** for novel input
- **Clarification** for partially recognized / ambiguous input
- **Reconciliation** for familiar-but-contradictory input
- **Engagement** for positive reactivation
- **Source verification** before update
- **Bounded local memory update** only on the matched BM trace

---

## Why This Matters

| Problem | Typical Risk | RGC Response |
|---|---|---|
| Unknown input | The system may answer before asking | `Cn` triggers identification |
| Partial recognition | The system may over-assume meaning | `Ca` triggers clarification |
| Familiar contradiction | The system may treat conflict as known | `Cc` triggers reconciliation |
| Weak source | The system may overwrite memory too easily | CSLV-compatible gate limits update |
| Poisoning attempt | False input may increase memory weight | Contradiction penalty can reduce weight |

The central contribution is the **Cc Blindspot**: a recognition-only monitor can classify familiar-but-contradictory input as known, while RGC treats it as a separate curiosity regime requiring verification.

---

## Core Equations

### Recognition Resonance

```text
R+_sim = max_i [ sim+(TM, BM_i) · intensity_i · weight_factor_i ]

sim+(a,b) = clip( (cosine_sim(a,b) + 1) / 2, 0, 1 )
```

### Four Curiosity Components

```text
Cn(t) = 1 - R+                                             # Novelty

Cc(t) = R+ · |V_TM(t) - V_BM| / V_max                       # Conflict

Ca(t) = 4 · R+ · (1 - R+) · I(R+ in [theta_lo, theta_hi])    # Ambiguity

Cr(t) = clip( W_old · max(V_BM, 0), 0, 1 )                  # Reward re-engagement
```

### Total Curiosity Score

```text
C_score(t) = alpha·Cn + beta·Cc + gamma·Ca + delta·Cr

where alpha + beta + gamma + delta = 1
```

### Source-Aware Memory Update

```text
CSLV_Conf          = S_val · I(S_val >= theta_val)
CSLV_Contradiction = max(0, 1 - C_att) · I(R_att >= theta_rel)

W_new = clip(
  W_old + eta · C_score · CSLV_Conf - mu · CSLV_Contradiction,
  0,
  1
)
```

| Source Type | Intended Update Behavior |
|---|---|
| Verified source | Weight may increase proportionally |
| Partial / below-threshold source | No confirmed increase; may slightly decrease if contradiction penalty applies |
| Irrelevant source | No update by default |
| Unreliable / poisoning source | Weight may decrease through contradiction penalty |

> The update applies only to the resonance-matched BM trace. Other memory traces remain unchanged.

---

## Ask–Verify–Update Flow

```text
Current input / TM state
        |
        v
TM-BM resonance
R+ computed
        |
        v
+--------------------------------------------------+
| RGC Gap Classification                            |
|                                                  |
| R+ low               -> Cn -> Identify / Ask     |
| R+ mid               -> Ca -> Clarify            |
| R+ high + valence gap -> Cc -> Reconcile / Verify|
| R+ high + positive V  -> Cr -> Re-engage         |
+--------------------------------------------------+
        |
        v
Ask / Clarify / Reconcile / Verify
        |
        v
CSLV-compatible source check
        |
        v
Bounded local BM update
```

---

## Validation Summary

> **Important:** These are controlled mechanistic simulations, not deployment benchmarks.  
> Tests use `sentence-transformers/all-MiniLM-L6-v2`, CPU execution, Google Colab, and no fine-tuning.

| Package | Purpose | Tests | Result |
|---|---:|---:|---:|
| **v1.2** | Core RGC validation | 5 | ✅ 5/5 PASS |
| **v1.3** | Advanced stress tests | 7 | ✅ 7/7 PASS |
| **v1.4b** | Focused Ca + CSLV validation | 5 | ✅ 5/5 PASS |
| **Total** | Controlled mechanistic tests | **17** | **✅ 17 PASS** |

---

### v1.2 — Core Validation

| Test | Description | Result |
|---|---|---|
| P1 | Novelty leads to ask-before-generating behavior | ✅ PASS |
| P2 | Few-shot BM consolidation: `R+=0.448`, best trace `trace_1` | ✅ PASS |
| P3 | Conflict curiosity: `Cc=0.342`, mode `RECONCILIATION / Conflict` | ✅ PASS |
| P4 | Local BM update isolation | ✅ PASS |
| P5 | Habituation + reactivation: `Reactivation Cc=0.325` | ✅ PASS |

---

### v1.3 — Advanced Stress Tests

| Test | Description | Result |
|---|---|---|
| A1 | Multi-novelty detection | ✅ PASS |
| A2 | Paraphrase recognition: `mean R+=0.690` | ✅ PASS |
| A3 | Conflict gradient: neutral `Cc=0.192` to conflict `Cc=0.416` | ✅ PASS |
| A4 | R-only ablation — Cc Blindspot | ✅ PASS |
| A5 | Local update isolation | ✅ PASS |
| A6 | Poisoning guard: `ΔW +0.0576`, `+0.0288`, `-0.0500` | ✅ PASS |
| A7 | Habituation + conflict reactivation: `Reactivation Cc=0.325` | ✅ PASS |

**Strongest ablation result:**

```text
R-only monitor -> KNOWN / NO ASK
RGC            -> RECONCILIATION / Conflict
```

---

### v1.4b — Focused Ca + CSLV Validation

| Test | Observed Behavior | Result |
|---|---|---|
| T1 | Ca forward: exact `Ca=0`, partial `Ca=0.855-0.958`, novel `Ca=0` | ✅ PASS |
| T2 | Recognition guard: familiar+contra -> reconciliation; novel+danger -> identification | ✅ PASS |
| T3 | CSLV gating: verified `ΔW=+0.075`; irrelevant `ΔW=0.000`; poisoning `ΔW=-0.080` | ✅ PASS |
| T4 | Local isolation: only the matched trace updates | ✅ PASS |
| T5 | R-only blindspot + CSLV response: `CSLV_Conf=0.90 -> ΔW=+0.083` | ✅ PASS |

**v1.4b T1 Ca detail:**

| Input Type | N | R+ Range | Ca Range | Mode |
|---|---:|---:|---:|---|
| Exact / familiar | 2 | `0.759-0.925` | `0.000` | Standard |
| Partial / ambiguous | 4 | `0.603-0.690` | `0.855-0.958` | Clarification |
| Novel / unrelated | 3 | `0.029-0.110` | `0.000` | Identification |

---

## Repository Structure

```text
RGC-1.0/
|
|-- README.md
|-- index.html
|-- CITATION.bib
|-- requirements.txt
|
|-- paper/
|   |-- RGC_v1_4b_Preprint.pdf
|   |-- RGC_v1_4b_Preprint.docx
|   `-- RGC_v1_4b_Journal.docx
|
|-- notebooks/
|   |-- RGC_v1_2_Core_Validation.ipynb
|   |-- RGC_v1_3_Advanced_StressTests.ipynb
|   `-- RGC_v1_4b_Focused_Ca_CSLV_Validation.ipynb
|
|-- results/
|   |-- RGC_v1_4b_T1_strong_Ca_forward_validation.csv
|   |-- RGC_v1_4b_T2_recognition_guarded_Cc.csv
|   |-- RGC_v1_4b_T3_CSLV_update_gating.csv
|   |-- RGC_v1_4b_T4_local_update_isolation.csv
|   |-- RGC_v1_4b_T5_R_only_blindspot_CSLV_response.csv
|   `-- figures/
|       `-- RGC_v1_4b_Ca_forward_validation.png
|
`-- companion/
    `-- related_frameworks.md
```

---

## How to Run

### Option 1 — Live Demo

Open:

```text
https://alimulhaqkhan-prog.github.io/RGC-1.0/
```

If GitHub Pages is not enabled yet:

```text
Repository -> Settings -> Pages -> Deploy from branch -> main -> /root -> Save
```

### Option 2 — Google Colab

1. Open Google Colab.
2. Upload a notebook from `notebooks/`.
3. Select **Runtime -> Run all**.
4. No GPU required.
5. No API key required.

### Option 3 — Local Environment

```bash
pip install -r requirements.txt
jupyter notebook notebooks/
```

### Requirements

```text
sentence-transformers>=2.2.0
numpy
pandas
matplotlib
jupyter
```

Embedding model:

```text
sentence-transformers/all-MiniLM-L6-v2
```

---

## Related Frameworks

RGC is part of the **AURA-X Ω** continuity-first research direction.

| Framework | Role | Link |
|---|---|---|
| **AURA** | TM-BM resonance and bounded meaning-state foundation | [aura-x-omega-2025](https://github.com/alimulhaqkhan-prog/aura-x-omega-2025) |
| **ACI** | Runtime continuity-health monitoring and Silent Alarm | [alim-continuity-index](https://github.com/alimulhaqkhan-prog/alim-continuity-index) |
| **CSLV** | Source-aware verification interface | Companion framework |
| **RGC** | Active curiosity and Ask-Verify-Update control | This repository |

Other companion frameworks such as ANIMA, RGCC-X+, CRM/AEC, Wajdan, and UPS may be explored as future extensions. They are not claimed as validated components of the present RGC simulation package.

---

## Intended Application Areas

RGC may be relevant to research and prototyping in:

- Memory-bearing AI agents and persistent-context assistants
- Enterprise copilots with long-term user memory
- Retrieval-augmented generation systems
- AI safety monitoring and hallucination-risk reduction
- Legal, medical, and compliance decision-support assistants
- Robotics and autonomous decision-support research
- AI governance and memory integrity research

The strongest practical use case is **memory update governance**: deciding when an AI system should ask, clarify, verify, reconcile, or update instead of blindly overwriting stored information.

---

## Non-Claims

RGC does **not** claim to be:

- A certified safety system
- A production-ready deployment
- A guarantee against hallucination
- A conscious, emotional, or sentient system
- A replacement for domain-specific safety validation
- A fully implemented CSLV L1-L4 pipeline
- A validated autonomous-vehicle, legal, medical, or financial decision system

The current validation package is limited to controlled mechanistic simulations. Broader evaluation across real agent logs, multiple embedding models, adversarial inputs, and deployed memory systems remains future work.

---

## Archive and DOI

This work is archived on Zenodo:

```text
DOI: 10.5281/zenodo.19976745
```

[Open Zenodo Archive](https://doi.org/10.5281/zenodo.19976745)

GitHub is the living development repository. Zenodo is the archival record for reproducibility. Scientific claims should be based on archived versions.

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
  note         = {Research-stage architecture; v1.2, v1.3, and v1.4b controlled
                  MiniLM-embedding validation packages; 17 mechanistic tests.}
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
