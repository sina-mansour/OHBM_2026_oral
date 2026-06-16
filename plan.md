# OHBM 2026 Oral Presentation — Slide Plan

**Title**: Mapping Organizational Gradients of Cortical Growth Using Spectral Normative Models
**Presenter**: Sina Mansour L.
**Date**: Wednesday, 17 June 2026, 15:45
**Room**: Agora
**Session**: Lifespan Development
**Duration**: 10 minutes + Q&A

---

## Structure Overview

| Time | Section | Slides | Purpose |
|------|---------|--------|---------|
| 0:00–1:00 | Hook & motivation | 1 | Why the audience should care |
| 1:00–2:30 | Background & gap | 1–2 | What's missing in the field |
| 2:30–3:30 | Method: SNM framing | 1 | Introduce the method, defer detail |
| 3:30–4:30 | Vertex-resolution normative outputs | 1 | What SNM produces |
| 4:30–5:00 | Growth gradients & framing | 1 | Introduce TGGs and the strata idea |
| 5:00–7:30 | TGG1–3 in detail | 3 | Characterise each gradient |
| 7:30–8:30 | Validation & interpretation | 2 | Why they're real and what they mean |
| 8:30–9:30 | Implications & retrogenesis | 1 | Conceptual punchline |
| 9:30–10:00 | Acknowledgements & close | 1 | Land smoothly |

Total: ~12 slides, ~50 seconds per slide on average.

---

## 1. Hook & motivation (0:00–1:00)

**Slide content**: Lifespan brain chart visual (Bethlehem-style trajectory of cortical thickness over the lifespan, or own version).

**Talking points**:

> Cortical thickness changes across the entire lifespan: it grows in childhood, peaks in adolescence, and steadily declines into old age. These changes reflect fundamentally different neurobiological processes, including synaptic pruning early on, myelination through adolescence, and atrophy in later life. The question that motivates this work: **are these processes organised macroscopically across the cortex, and if so, how?**

**Notes to self**:
- Open with the question stated explicitly.
- Don't dwell; set up and move on.

---

## 2. Background & gap (1:00–2:30)

**Slide content**: Two-part conceptual figure:
- (a) Established links between cortical thickness in young adulthood and cortical hierarchies: the principal functional gradient (Margulies 2016), the SA axis (Sydnor 2021), and the principal axis of gene expression (Dear 2024).
- (b) The open question: how do these associations evolve dynamically across development and aging?

**Talking points**:

> Previous work has established that, in young adulthood, cortical thickness aligns closely with multiple organisational hierarchies of the cortex: the principal functional connectivity gradient, the sensorimotor-to-association axis, and the principal axis of cortical gene expression. These convergent hierarchies point to a deeply structured organisation of cortical biology.
>
> But what we don't yet know is how these associations evolve dynamically across the lifespan; how developmental processes like synaptic pruning, and aging processes like cortical atrophy, reshape or reinforce these hierarchies over time.
>
> Normative modelling has become a powerful framework for population-level inference of brain phenotypes, much like growth charts in paediatrics. But applying it at vertex resolution across the entire lifespan runs into two challenges: it's computationally prohibitive, and conventional approaches treat each vertex independently, ignoring the spatial organisation of the cortex.

**Notes to self**:
- This is where the gap is most explicit; make sure the audience leaves this section knowing what is missing.
- Acknowledge prior literature briefly but firmly; don't over-credit.
- End by setting up the methodological solution.

---

## 3. Method: Spectral Normative Modelling (2:30–3:30)

**Slide content**: A compact version of the SpectraNorm framework figure (diffusion MRI to tractography to connectome to eigenmodes to spectral normative fitting), alongside a callout of the dataset size and visual references to the poster and preprint (logos / QR thumbnails).

**Talking points**:

> To address this, we recently developed a framework we call **Spectral Normative Modelling**, or **SNM**. It builds on the strengths of conventional normative modelling but introduces a graph-spectral representation of the cortex, which makes vertex-resolution inference both computationally tractable and spatially aware.
>
> In brief: we represent the cortex using a small set of eigenmodes of the structural connectome, project cortical thickness from each scan into that basis, and fit a hierarchical Bayesian normative model jointly across modes. This was applied to **78,405 quality-controlled scans across 30 datasets and 189 sites**, spanning ages 5 to 95.
>
> I won't be going through the method in any more detail today, since the goal of this talk is to explore what we *learn* from the resulting normative model. But I'm very happy to discuss the methodology with anyone interested after the session, and full details are in the companion poster and our preprint.

**Notes to self**:
- Keep this short; the goal is to land the method name, the conceptual idea, and the dataset size, then move on.
- The "happy to discuss after" framing is friendly without being dismissive; respects the audience's time and signals confidence in the method.
- Call out 78,405 explicitly; it's a credibility multiplier.
- Make sure the poster/preprint references are visible on the slide so curious viewers can locate them.

---

## 4. From normative outputs to growth gradients (3:30–7:30)

### 4a. Vertex-resolution normative estimates (3:30–4:30, ~60 seconds)

**Slide content**: A figure showing SNM-derived vertex-resolution outputs across the lifespan: mean cortical thickness, inter-individual variability (standard deviation), and annual % rate of change. Five age snapshots (e.g. 5, 20, 40, 65, 85 years) shown as brain maps for each estimate.

**Talking points**:

> Once SNM is trained, the model gives us vertex-resolution normative estimates of three things at any age: the **mean** cortical thickness, the **inter-individual variability** around that mean, and crucially, the **annual percent rate of change**.
>
> What you see here is how those estimates look at different points across the lifespan. Already from these maps, you can see hints of organisational structure: regional differences in mean thickness, regions where variability concentrates, and regions undergoing the fastest change at different ages.

**Notes to self**:
- Let the figure breathe; this is a chance for the audience to *see* what the normative model produces before we start decomposing it.
- Don't yet talk about gradients; just establish the outputs.

### 4b. Growth gradients & rest-of-talk framing (4:30–5:00, ~30 seconds)

**Slide content**:
- (top) PCA summary visual: scree plot or a note that 3 components explain >90% of variance in vertex-wise trajectories.
- (bottom) Three brain maps showing TGG1, TGG2, TGG3 side by side.
- (annotation) A small diagram illustrating the concept of defining strata along a gradient and querying the normative trajectory within each stratum.

**Talking points**:

> To make sense of these vertex-wise trajectories, we applied PCA. Three components together explain over 90% of the variance, and we call these the **Thickness Growth Gradients**: TGG1, TGG2, and TGG3.
>
> In the rest of the talk, I'll characterise the information captured by each gradient, and explore how each links to our existing understanding of cortical hierarchies.
>
> One thing worth flagging: SNM lets us probe arbitrary post-hoc normative questions on the pretrained model. For instance, to interpret a gradient, we can define spatially delineated strata along it and query the normative trajectory within each stratum. That's the engine behind the trajectory plots you'll see on the next few slides.

**Notes to self**:
- This is a busy slide; let the visual carry the load while the spoken framing sets up what's coming.
- The strata point is a brief flag; don't dwell, but it makes the trajectory plots in 4c–4e meaningful.

### 4c. TGG1: stable global organisation (5:00–5:50, ~50 seconds)

**Slide content**: Brain map of TGG1, normative trajectories stratified along TGG1, scatter with mean cortical thickness.

**Talking points**:

> **TGG1** captures the stable, age-invariant axis of cortical thickness organisation: paralimbic and association cortices at one end, primary sensorimotor at the other. It correlates with mean cortical thickness at *r* = 0.91, recapitulating the canonical sensorimotor-to-association hierarchy across the entire lifespan.

### 4d. TGG2: aging-related thinning (5:50–6:40, ~50 seconds)

**Slide content**: Brain map of TGG2, normative trajectories stratified along TGG2, scatter with ATD.

**Talking points**:

> **TGG2** isolates aging-related thinning, concentrated in premotor and superior temporal regions. It correlates strongly with our Aging Thickness Decline map (*r* = 0.88) and aligns with a transcriptional component enriched for metabolic processes.

### 4e. TGG3: developmental pruning (6:40–7:30, ~50 seconds)

**Slide content**: Brain map of TGG3, normative trajectories stratified along TGG3, scatter with DCP.

**Talking points**:

> **TGG3** captures early-life developmental pruning: sharp thinning in primary and unimodal cortex during ages 5 to 20, correlating with our Developmental Cortical Pruning map at *r* = −0.76. It aligns with a gene expression component enriched for synaptic plasticity.

**Notes to self**:
- Use the **same visual template** for slides 4c–4e: same panel layout, same colour scheme; only the content changes.
- This repetition is what makes the three-gradient story land.
- The trajectory plots in each slide are concrete demonstrations of the strata-querying idea introduced in 4b.

---

## 5. Validation & interpretation (7:30–8:30)

### 5a. Cytoarchitectonic alignment

**Slide content**: Mesulam / Von Economo violin plots, with FDR-corrected significance annotations.

**Talking points**:

> These gradients aren't statistical artefacts. They align with established cytoarchitectonic frameworks, both Von Economo classes and Mesulam's laminar hierarchy, with all three omnibus tests surviving FDR correction using BrainSMASH spatial-autocorrelation-preserving nulls.

### 5b. Cluster convergence

**Slide content**: Cluster heatmap (from the poster) showing TGG and hierarchy convergence.

**Talking points**:

> They also cluster cleanly with independent cortical hierarchies: the sensorimotor-association axis, functional gradients, and transcriptional components. Each TGG anchors a distinct cluster of converging modalities, suggesting these are genuine organisational axes of cortical biology, not just statistical decompositions.

**Notes to self**:
- This is the credibility section; lead with the rigour.
- Mention BrainSMASH and FDR explicitly to anticipate methods questions.

---

## 6. Implications & retrogenesis (8:30–9:30)

**Slide content**: Scatter plot of DCP vs. ATD showing the anticorrelation, alongside an annotated brain map highlighting the spatial pattern.

**Talking points**:

> One striking result ties this all together: the regions that undergo the most pruning in childhood are *not* the same regions that thin the most in aging. Quite the opposite, they're **anticorrelated**.
>
> This extends the **retrogenesis hypothesis**: regions relatively spared by developmental pruning appear to be more vulnerable to atrophy in later life. TGG3 places this hypothesis on a quantitative, lifespan-wide footing, and points to specific gene expression patterns that may underlie the asymmetry.

**Notes to self**:
- This is the line the audience will remember; land it firmly, don't rush.
- Pause briefly after "anticorrelated".

---

## 7. Close (9:30–10:00)

**Slide content**: Summary slide with the three TGG brain maps, group affiliations, acknowledgements, and a QR code linking to the OHBM_2026 site.

**Talking points**:

> To summarise: Spectral Normative Modelling lets us decompose lifespan cortical thickness into three interpretable, biologically grounded gradients: stable organisation, aging-related thinning, and developmental pruning. The latter two are anticorrelated in a way that supports retrogenesis at the gradient level.
>
> Huge thanks to my collaborators at NUS and Melbourne, particularly Thomas Yeo and Andrew Zalesky, and to the Lifespan Brain Chart Consortium for making this scale of analysis possible. Full poster, code, and details are at the QR code. Happy to take questions.

---

## Pacing & rehearsal notes

- **Practise full run-throughs out loud**; most first attempts overshoot by 1–2 minutes.
- Have a **"skip slide"** mentally marked (e.g. slide 4b condensed, or slide 5b cut) to drop if running long.
- Each slide should have at most 2 clear takeaways; if more, decide which one to point at.
- Test the "one sentence per slide" rule: can I say what each slide is *for* in one sentence?

---

## Anticipated Q&A

- **Why structural connectome eigenmodes vs. other bases** (geometric eigenmodes, parcellations, ICA components)?
- **How does the AD application fit in**: is the AD heterogeneity result part of this talk or the poster only?
- **Relationship to the Sydnor SA axis** specifically: is TGG1 just the SA axis under a different name?
- **Sensitivity to the number of eigenmodes**: what happens with 500 vs. 2000 modes?
- **Why these three age windows** (5–20 for DCP, 40–95 for ATD)?
- **Cross-validation** across cohorts: does TGG decomposition replicate in held-out datasets?

---

## Related materials

- Companion poster: https://sina-mansour.github.io/OHBM_2026/poster/
- Preprint: *Spectral Normative Modeling of Brain Structure* (in revision, Nature Neuroscience)
- SpectraNorm package: https://sina-mansour.github.io/spectranorm/
- BrainHack project: https://sina-mansour.github.io/OHBM_2026/brainhack/