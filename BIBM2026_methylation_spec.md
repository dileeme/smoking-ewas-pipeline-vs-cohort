# BIBM 2026 Workshop Paper — Project Specification

**Working title:** *Is the Pipeline the Confound? Decomposing Analytical-Choice Variance Against Cross-Cohort Biological Variance in Smoking EWAS*

**Target:** IEEE BIBM 2026, Dallas — **workshop track** (IEEE Xplore indexed). Conference 1–4 Dec 2026. Workshop deadlines not yet published; **plan against a pessimistic ~early-September 2026 deadline.** Main-conference deadline is 5 Jul 2026 — not the target, but the workshop list will post against it; track it.

**Author:** solo. **Effort assumption:** 20–40 hrs/week. **Tooling:** R / Bioconductor (minfi, limma) — same ecosystem as your prior DE work. **Data:** public GEO only. **Independence:** zero FHE / CKKS / PRS / beacon / membership-inference content. This is a methylation reproducibility study and shares no surface with the Beacon or FHE-journal papers.

---

## 1. The one-sentence contribution

When two independent EWAS cohorts study the *same* exposure (smoking), is the disagreement introduced by **analytical-pipeline choice** larger than, comparable to, or smaller than the disagreement introduced by **cohort sampling itself** — and does that ratio depend on signal strength?

Framed as a decision for the field: *for EWAS reproducibility, is standardizing pipelines or harmonizing cohorts the higher-priority lever?* Nobody has isolated this because every prior pipeline-sensitivity study runs on a single dataset.

This is structurally the Beacon paper's move: take two known effects (pipeline sensitivity is known; cross-cohort EWAS irreproducibility is known) and isolate a quantity nobody has measured (their *relative* magnitude), with negative controls as the rigor mechanism.

---

## 2. STEP 0 — The novelty hard-stop (GATE, not a suggestion)

**You do not run a single line of analysis until this gate is passed.** You accepted this as mandatory; it is written here as a gate with an explicit go/no-go criterion so it cannot be hand-waved under time pressure.

**Time budget:** 1 focused day (~6–8 hrs). Not more — this is a check, not a literature review.

**The exact question to answer:** *Has anyone published a decomposition of analytical-pipeline variance vs. cross-cohort biological variance in array-based EWAS, for any phenotype?*

**Where to look (in order):**
1. Google Scholar + PubMed: `EWAS reproducibility pipeline variance cross-cohort`, `methylation analytical flexibility multiverse`, `DNA methylation pipeline cohort replication smoking`.
2. The groups most likely to have done it: Mill, Relton, Houseman, Hovarth, the LIMIT authors (Reed/Moore), the ARIES/ALSPAC consortium, the EWAS Catalog team.
3. Forward-citation check on the LIMIT paper (biorxiv 2022 / PMC9901304) and the doc-40 pipeline-evaluation paper — anyone who extended either to multiple cohorts.
4. "Multiverse analysis" / "vibration of effects" methodology applied to methylation specifically.

**GO criterion:** No paper decomposes analytical vs. cross-cohort variance as competing reproducibility sources in EWAS. (Single-cohort sensitivity studies existing = still GO; that is the gap.)

**NO-GO criterion:** A paper already quantifies analytical-vs-cohort variance partition for EWAS. → Fall to the **fallback axis** (Section 8), do not proceed on the primary framing.

**Output of Step 0:** a 5-line written verdict (GO / NO-GO + the 3 closest papers and why they don't close the gap). Keep it — it becomes the related-work positioning paragraph.

---

## 3. Data (smoking anchor — confirmed)

Smoking is chosen because it is the most replicated exposure in EWAS, has a known strong-signal locus structure (AHRR cg05575921, GFI1, F2RL3, etc.) **and** a long tail of weak, less-replicated probes — giving you both ends of the signal-strength axis within one phenotype.

**Need: ≥3 independent public 450K/EPIC blood smoking datasets, case/control or current-vs-never.** Candidate GEO families (verify accessions and sample sizes during Step 0 / Week 1 — do not trust this list blindly, it is a starting point):
- Large population blood methylation series with smoking metadata (GEO has several; the EWAS Catalog and the Joehanes 2016 consortium meta-analysis reference list is the fastest index to source individual contributing datasets).
- Target: cohorts of n ≥ 100 each, same tissue (whole blood), 450K or EPIC.
- Minimum viable: **2 independent cohorts.** Stretch: 3–4.

**Hard data rules:**
- Same tissue across cohorts (whole blood only — mixing tissue destroys the comparison).
- Raw IDAT files available (you need to run the pipelines yourself; processed beta matrices defeat the purpose).
- Smoking status defined compatibly (current vs never; drop "former" or model it separately).

If raw IDATs are not available for ≥2 clean cohorts: this is a real risk. Surface it in Week 1, not Week 6. Fallback is Section 8.

---

## 4. Design

**Analytical-choice grid (the "pipeline" axis).** Keep it deliberately small and defensible — 3 axes, not 10:
- Normalization: {noob, BMIQ, functional normalization} — 3 levels.
- Cell-type adjustment: {adjusted (Houseman), not adjusted} — 2 levels.
- Batch handling: {ComBat, model covariate} — 2 levels.

= 12 pipelines per cohort. This is enough to be a credible "analytical multiverse" and small enough to run solo.

**The variance decomposition (the contribution).** For each probe, you have a smoking effect estimate (Δβ or moderated t from limma) under each (cohort × pipeline) combination. Partition the variance in the effect estimate into:
- **Analytical variance:** spread across the 12 pipelines, within a cohort.
- **Cohort variance:** spread across cohorts, within a fixed pipeline.
- Report the ratio, genome-wide and stratified by signal strength (strong/known smoking probes vs. weak/tail probes).

**Negative controls (carried directly from your Beacon methodology — this is the rigor mechanism, not optional):**
- **Permuted-label control:** shuffle smoking status; both variances should collapse toward null. Confirms the signal is real, not pipeline artifact.
- **Known-positive control:** the canonical AHRR/F2RL3/GFI1 probes — these should be stable across *both* axes. If they're not, the pipeline is broken, not the biology.
- **Random-group control:** assign fake groups; quantifies false-positive rate as a function of pipeline choice (the LIMIT paper's spurious-effect idea, applied across cohorts instead of within one).

**Headline finding (whichever way it falls — all three are publishable):**
- If analytical > cohort variance for weak signals: "pipeline standardization is the higher-priority reproducibility lever for the subtle effects that dominate disease EWAS."
- If cohort > analytical: "pipeline panic is overstated; cohort heterogeneity dominates — harmonization beats standardization."
- If it crosses over at a signal-strength threshold: that threshold *is* the paper (the Beacon-style phase-transition analogue).

You do not need a particular outcome. The decomposition is the contribution regardless of direction. This is what makes it safe.

---

## 5. Scope: minimum-viable vs. stretch

**Minimum viable (this is the paper that gets submitted):**
- 2 independent smoking cohorts, whole blood, raw IDATs.
- 12-pipeline grid.
- Variance decomposition, genome-wide + strong/weak stratification.
- All 3 negative controls.
- 6 pages, IEEE format.

**Stretch (only if Week 12 is on schedule):**
- 3rd/4th cohort.
- A second exposure (e.g., a disease phenotype) to test whether the analytical/cohort ratio generalizes — this is genuinely strong if it lands but is *cut first* if time slips.

**Cut order under pressure:** drop stretch cohort → drop 2nd exposure → narrow grid to 6 pipelines (2 norm × keep cell-type × keep batch). **Never cut: the negative controls.** They are what gets it past expert reviewers; everything else is volume.

---

## 6. Week-by-week (against pessimistic early-Sept deadline; ~16–18 weeks from late May)

- **Week 0 (now):** Step 0 novelty gate. GO/NO-GO before anything else.
- **Weeks 1–2:** Source and verify the cohorts. Confirm raw IDATs, sample sizes, compatible smoking metadata. This is the highest-risk phase — if clean data isn't there, you find out now and pivot to Section 8, not in month 2.
- **Weeks 3–5:** Build one clean pipeline end-to-end (minfi → noob → limma DMP) on one cohort. Reproduce a known smoking hit (cg05575921). This is your confidence anchor and reuses your DE muscle memory.
- **Weeks 6–9:** Scale to the full 12-pipeline grid across both cohorts. Expect this to be the slow, debug-heavy stretch (the analogue of the edgeR/DESeq2 weeks in the earlier plan). Budget the buffer here.
- **Weeks 10–12:** Variance decomposition + the 3 negative controls. This is the actual contribution; protect this time.
- **Weeks 13–14:** Figures, headline finding, results consolidation.
- **Weeks 15–16:** Write. Methods + results first (your proven order). IEEE workshop format, 6 pages.
- **Week 17:** Untouched buffer. Submit before the deadline, not on it.

**Single biggest failure mode:** Weeks 1–2, data availability. If ≥2 cohorts with raw IDATs and compatible metadata don't materialize, the primary design is dead and you go to Section 8 *immediately* — do not try to force it with processed beta matrices.

**Second failure mode:** Weeks 6–9 grid debugging overruns into the decomposition window. Mitigation: if behind at end of Week 9, cut to the 6-pipeline grid (Section 5 cut order) rather than compressing the decomposition or controls.

---

## 7. BIBM track placement

Primary: **3.g Epigenomics.** Secondary viable: **5.i Biomarker Discovery** (frame as "reliability of methylation biomarkers across analytical pipelines"). Both are lower-crowd than 5.a/6.a (ML/AI). Do **not** submit to the ML/AI tracks — the contribution is methodological rigor, not a model.

---

## 8. Fallback axis (if Step 0 is NO-GO, or data fails in Weeks 1–2)

**Probe-reliability-stratified pipeline sensitivity within a single large cohort.** Document 35 established that low-reliability EPIC probes have worse cross-study effect-size consistency. The narrower, still-defensible question: *does analytical-choice instability concentrate in low-reliability probes, and can a reliability filter substantially recover pipeline-robustness?* Single cohort, so no cross-cohort data dependency — strictly easier to source. Weaker contribution than the primary, but publishable at a workshop and shares the same tooling and negative-control design. This is the safety net; it is not the goal.

---

## 9. The paragraph to put in front of your guide (sanity-check before Week 1)

> I'm proposing a solo BIBM 2026 workshop paper on EWAS reproducibility. Prior work shows analytical-pipeline choice affects differential-methylation results, but always within a single cohort. I want to decompose, for smoking EWAS across ≥2 independent public 450K cohorts, how much of the disagreement in smoking-associated probes comes from pipeline choice versus cohort sampling, stratified by signal strength, with permuted-label / known-positive / random-group negative controls. The deliverable is a 6-page IEEE workshop paper; data is public GEO; tooling is R/Bioconductor. Before I start I'll spend a day confirming nobody has already done this variance decomposition. Does the framing hold, and is there a reason the cross-cohort comparison would be invalid that I'm not seeing?

Get a real answer to that last clause from someone who knows EWAS before Week 3. It's the cheapest possible insurance against a structural flaw I can't see from here.

---

## 10. Honest risk ledger (read this before committing the next 16 weeks)

1. **Novelty (medium):** the cross-cohort-variance gap looks open after focused searching but is not confirmed. Step 0 exists precisely for this. Do not skip it.
2. **Data (medium-high):** the whole primary design depends on ≥2 clean same-tissue smoking cohorts with raw IDATs. This is the thing most likely to kill it. Front-loaded to Weeks 1–2 by design.
3. **Domain learning curve (low-medium):** methylation arrays are a new domain but the same Bioconductor world as your DE work. "New domain, familiar tooling" — the manageable tier you self-assessed, not a cold start.
4. **Scope discipline (medium):** the documented pattern across this project's planning has been scope drift toward bigger ideas. The minimum-viable definition and cut order in Section 5 exist to bound this. Hold the line at MVP; the stretch exists to be cut.
5. **Timeline (low, conditional):** ~16–18 weeks at 20–40 hrs/week is sufficient for the MVP *if* Step 0 and data sourcing pass quickly. It is not sufficient if you spend a month deciding to start. The deferring has to end at Step 0.
