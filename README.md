# 🏭 Defect Reduction: Where to Aim the Next Improvement Cycle

A question-first **Six Sigma DMAIC** analysis that answers a single operational decision:

> *A plant has one improvement team and one cycle. Its production runs are flagged high-defect 84% of the time. **Where should the team aim?***

[![Live Dashboard — Tableau](https://img.shields.io/badge/Live_Dashboard-Tableau-E97627?logo=tableau&logoColor=white)](https://public.tableau.com/app/profile/brian.ketchens/viz/CIDefectReductionDashboard/Dashboard1)

> ⚠️ **Methodology demonstration.** This analysis runs on a *synthetic* manufacturing dataset (Kaggle). The findings below are real properties of that data, but they are not claims about any actual plant. What is being demonstrated is the **method**: how to screen candidate drivers, reject noise, isolate the vital few, and convert them into countermeasures a plant manager can act on.

---

## 📊 The Dashboard

**[→ View the live interactive dashboard on Tableau Public](https://public.tableau.com/app/profile/brian.ketchens/viz/CIDefectReductionDashboard/Dashboard1)**

Four linked views: an opportunity-sizing KPI ribbon, a vital-few driver screen, a dose-response chart, and a maintenance × quality heatmap — closing with an explicit, written recommendation.

---

## 🎯 Define — The Problem Statement

| | |
| :--- | :--- |
| **Stakeholder** | Plant Quality Manager |
| **Decision to make** | Where to deploy one improvement team for one cycle |
| **Current state** | 84% of production runs flagged high-defect |
| **Best observed state** | 47% — runs operating with zero adverse conditions |
| **Gap to target** | **37 percentage points** |
| **Scope** | 3,240 production runs · 16 candidate operational factors |

The gap between the plant's *current* performance and its *own best observed* performance is the opportunity. The analytical question is which controllable conditions move a run from one band to the other.

---

## 📏 Measure & Analyze — Screening the Vital Few

All sixteen candidate factors were screened for their ability to separate high-defect runs from acceptable ones. **Twelve were rejected as statistical noise** (|correlation| < 0.05). Three survived.

| Factor | Separation strength | Verdict |
| :--- | :--- | :--- |
| **Maintenance Hours** | 0.297 | ✅ Vital few |
| **Quality Score** | 0.199 | ✅ Vital few |
| **Production Volume** | 0.129 | ✅ Vital few (weak) |
| Stockout Rate | 0.041 | ❌ Noise |
| Supplier Quality | 0.038 | ❌ Noise |
| Energy Efficiency | 0.035 | ❌ Noise |
| *…9 more factors* | < 0.03 | ❌ Noise |
| ~~Defect Rate~~ | 0.246 | ⛔ **Excluded — tautological** (a component of the target) |

**Two screening decisions worth calling out**, because they *are* the analysis:

1. **`DefectRate` was excluded despite scoring second-highest.** It is a component of the target variable, so including it would be leakage dressed up as insight. A high correlation is not automatically a finding.
2. **Twelve factors were actively rejected, not ignored.** Reporting what *doesn't* drive defects is as valuable to a plant manager as reporting what does — it stops the team from chasing supplier quality or energy efficiency when the data says they're inert.

### The dose-response relationship

Defining each surviving driver as an adverse condition (maintenance ≥ 10 hrs, quality score < 82, volume ≥ 520 units), the flag rate climbs monotonically as conditions stack:

| Adverse conditions present | High-defect flag rate | Runs |
| :--- | :--- | :--- |
| 0 | **47.1%** | 274 |
| 1 | 79.0% | 1,078 |
| 2 | 91.0% | 1,349 |
| 3 | **95.5%** | 539 |

A clean dose-response curve is strong evidence the drivers are real and not artifacts of a single cut.

### Maintenance hours dominate

Cross-tabulating the two strongest drivers shows maintenance doing most of the work — crossing the 10-hour threshold pushes flag rates into the 90s *regardless* of quality score:

| | Quality ≥ 82 | Quality < 82 |
| :--- | :--- | :--- |
| **Maintenance ≥ 10 hrs** | 93% | 96% |
| **Maintenance < 10 hrs** | 67% | 85% |

---

## 🔍 The Counterintuitive Root Cause

**More maintenance hours are associated with *more* defects, not fewer.**

The naive reading — *"cut maintenance hours"* — is exactly wrong, and it is the trap this analysis is built to avoid.

Maintenance hours are a **symptom, not a cause**. Equipment that demands 12+ hours of maintenance is equipment that is chronically failing — and chronically failing equipment also produces defective output. The maintenance hours are the *signature* of the underlying problem, not the problem itself. Cutting them would remove the smoke while leaving the fire.

> **The countermeasure therefore targets equipment reliability, not the maintenance calendar.**

This is the distinction between correlation and causation, and it is where a root-cause analysis either earns its keep or produces an actively harmful recommendation.

---

## 🔧 Improve — Countermeasures

**Short-term (this cycle):**

1. **Target equipment reliability.** Flag any run exceeding 10 maintenance hours for a reliability review; pilot predictive/preventive maintenance on the worst-offending assets. Start with the 16.6% of runs sitting in the all-three-conditions band — they run 95.5% flagged.
2. **Enforce the quality gate.** Hold runs scoring below 82 for inspection before release.
3. **Review high-volume capacity.** Volume is a distant third driver; investigate whether ≥520-unit runs are outstripping line capacity, but do not spend the team's cycle here.

**Prevent recurrence (Control):**

- Adopt the three thresholds as standing operating targets.
- The dashboard itself becomes the control tool — it flags runs *entering* the high-risk band so the team can intervene before defects are produced, rather than after.
- **Do not** chase the twelve noise factors. This analysis is as much a map of where *not* to spend improvement capacity.

**Projected impact:** moving runs into the clean operating band is associated with the high-defect flag rate falling from **84% → 47%** — a 37-point (≈44% relative) reduction.

---

## 📁 Repository Structure

```text
six_sigma_manufacturing/
├── data/
│   ├── manufacturing_defect_dataset.csv     # Raw Kaggle source
│   ├── manufacturing_defects_prepped.csv    # + derived flags & risk-factor count
│   └── factor_importance.csv                # Screening results for all 16 factors
├── tableau/
│   └── CI_Defect_Reduction_Dashboard.twbx   # Packaged Tableau workbook
├── assets/
│   └── ci_defect_card_1.html                  # Portfolio card markup
└── README.md
```

### Derived fields

The prepped dataset adds six engineered columns used directly by the dashboard:

| Field | Definition |
| :--- | :--- |
| `MaintenanceFlag` | High (≥10 hrs) / Normal (<10 hrs) |
| `QualityFlag` | Below Gate (<82) / At-or-Above Gate (≥82) |
| `VolumeFlag` | High (≥520 units) / Normal (<520 units) |
| `RiskFactorCount` | Count of adverse conditions present (0–3) |
| `RiskZone` | High-Risk Zone (all 3) / Outside Zone |
| `DefectStatus_Label` | Human-readable target label |

---

## 🧠 What This Project Demonstrates

- **Question-first analysis.** The work begins with a stakeholder and a decision, not with a dataset and a chart library.
- **Disciplined screening.** Sixteen factors in, three out — with the rejected twelve reported, and one high-scoring variable excluded on principle for leakage.
- **Correlation-vs-causation judgment.** The headline driver points to a countermeasure that is the *opposite* of the naive reading.
- **Countermeasures, not observations.** The deliverable is a prioritized action plan with an estimated impact, not a description of the data.
- **Honest framing.** The synthetic nature of the data is disclosed up front, keeping the focus on transferable method.

---

## 🛠️ Tools

Tableau Public (calculated fields, dashboard assembly) · Python / pandas (data preparation and factor screening) · Six Sigma DMAIC framework

## 📚 Data Source

Predicting Manufacturing Defects Dataset (Kaggle) — synthetic. 3,240 production runs, 17 original columns.
