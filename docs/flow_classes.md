# Section C: Flow Classes

*BIFO Specification v0.02*

Flow classes define admissible biological transformations over entities and states. The primary organizational backbone follows a hierarchical flow from genetic sequence to phenotype to disease:

```
G + CH → RNA → P(state) → PW → C → PH → DS
```

---

## Central dogma transformations

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Transcription | G + CH → RNA(coding) | Chromatin-gated gene expression |
| Translation | RNA(coding) → P | Protein synthesis |

---

## Chromatin regulation

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Chromatin Control | CH → RNA | Transcription gating |
| Epigenetic Modification | P or RNA → CH | Chromatin modification |
| Chromatin Topology | CH → CH | Three-dimensional genomic interactions |

---

## RNA-mediated regulation

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| RNA Regulation | RNA(regulatory) → RNA(coding) | Repression or degradation |
| RNA Processing | RNA(processing) → RNA(coding) | Splicing and maturation |
| Translation Support | RNA(structural) → P | Ribosomal and tRNA function |
| RNA-RNA Binding | RNA(regulatory) + RNA(target) → RNA(target') | Sequence-specific hybridization |
| RNA Silencing (degradation) | RNA(regulatory) → RNA(target') | Target degradation |
| RNA Silencing (repression) | RNA(regulatory) → RNA(target') | Translation inhibition |
| RNA Stabilization | RNA(regulatory) → RNA(target') | Increased stability |
| RNA Processing via RNA | RNA(processing) → RNA(target') | Editing or maturation |

---

## Protein and complex signaling

Stable assemblies are represented as macromolecular complexes (CM), while transient interactions are represented as direct state transformations between proteins without persistent complex formation. See [entity_classes.md](entity_classes.md) for the formal complex boundary criterion.

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Signal Transduction | P(state) → P(state') | PTM-driven signaling |
| Transient Interaction Signaling | P(state) → P(state') | Short-lived signaling interaction |
| Complex Formation | P → CM | Assembly of complexes |
| Complex Function | CM → P or PW | Functional execution |
| Protein Interaction | P ↔ P | Binding interactions |
| Pathway Contribution | P or CM → PW | Participation in biological programs |
| Protein-DNA Regulation | P or CM + CH(region) → RNA(coding) | Transcription factor binding regulates transcription |
| Signal Termination | P(state) → P(state₀) | Reversal or attenuation of signaling |
| Negative Feedback | downstream → upstream | Regulatory feedback within signaling pathways |

---

## Genetic modulation of transcription

Genetic regulatory modulation operates by altering sequence-dependent binding and chromatin accessibility, thereby modifying protein-DNA regulatory interactions.

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Genetic Regulatory Modulation | G(variant) → CH or RNA | Sequence variation alters transcriptional output |

---

## Small-molecule transformations

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Biochemical Transformation | SM(metabolic) → SM(metabolic) | Substrate-product conversion |
| Metabolic Coupling | P or CM ↔ SM(metabolic) | Enzyme-mediated transformation |
| Energetic Constraint | SM(energetic) → system state | Contextual dependency on energetic cofactors |

---

## Redox and electron transfer

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Electron Transfer | SM(redox) → SM(redox) | Redox transitions |
| Electrochemical Coupling | SM(redox) → ION | Gradient generation |
| Chemiosmotic Coupling | ION → SM(energy) | ATP synthesis |

---

## Small-molecule signaling and perturbation

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Molecular Signaling | SM(signaling) → P or RNA or CH | Endogenous signaling |
| Perturbational Effect | SM(perturbational) or X → system state | External modulation |
| Metabolic Regime Control | SM(environmental) → system state | Oxygen and carbon dioxide constraints |
| Nuclear Signaling | SM(signal) → P(state, location) → CH → RNA | Ligand-driven transcriptional activation |
| Receptor-Mediated Signaling | SM(signal) → P(receptor) → P(state') | Ligand binding activates receptor |

---

## Electrochemical signaling

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Ion Channel Flow | P(state) → ION | Channel-mediated gradient change |
| Ionic Propagation | ION → ION | Spatial propagation of gradients |
| Electrochemical Signaling | ION → P or RNA or PW or C | Downstream signaling |

---

## Spatial and transport processes

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Intracellular Transport | entity(locᵢ) → entity(loc) | Movement between compartments |
| Vesicular Transport | entity → entity | ER-Golgi-membrane trafficking |
| Nuclear Transport | entity ↔ nucleus | Import and export |
| Organelle Targeting | entity → organelle | Localization |
| Compartmental Processing | entity(loc) → entity(state', loc) | Compartment-restricted processing |
| Secretion | intracellular → extracellular | Export |
| Uptake | extracellular → intracellular | Import |
| Extracellular Transport | regionᵢ → region | Diffusion or circulation |
| Source-Sink Exchange | source ↔ sink | Production and clearance |
| Molecular Degradation | entity → sink | Removal of molecules from system |
| Proteasomal Processing | P → degraded state | Ubiquitin-proteasome degradation |
| Autophagic Processing | CM / organelle → degraded state | Bulk degradation via autophagy |

---

## Cell-cell communication

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Cell-Cell Communication | C₁ → C₂ | Intercellular signaling |

---

## Cellular state and progression

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Chromatin → Cell | CH → C | Defines attractor-like identity |
| Functional Transition | PW or P or SM → C | State transitions |
| Cell Cycle Control | P or PW → cell_cycle_state | Cell cycle regulation |
| Cell Cycle Constraint | cell_cycle_state → system state | Phase-dependent gating |
| State Progression | state(t) → state(t+1) | Temporal evolution |

---

## Mechanotransduction

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Mechanotransduction | MECH → P or CH or RNA or C | Physical signaling |

---

## Phenotype and disease

| Flow | Source → Target | Meaning |
|------|----------------|---------|
| Cell → Phenotype | C → PH | Trait emergence |
| Molecular → Phenotype | P or PW → PH | Molecular effects |
| Phenotype Aggregation | PH → DS | Disease definition |

---

## Observational and inference relationships

Observational relationships inform model construction and weighting but are **not propagated as mechanistic flows** unless explicitly validated. The mechanistic/observational distinction is load-bearing: flows in this table must not be used as propagation paths in BIFO-conditioned graphs. They may be used as priors, weights, or constraints on mechanistic flows, but do not themselves carry biological state.

Examples of data types that produce observational rather than mechanistic edges: single-cell clustering assignments, ATAC-seq accessibility correlations, spatial transcriptomics co-occurrence, multi-omic integration outputs, and topological graph proximity scores.

| Flow Class | Source → Target | Meaning |
|------------|----------------|---------|
| Observational Inference | CH / RNA / S → C | Cell configuration inferred from multi-omic measurements |
| Observational Association | CH / RNA / P → PW / PH | Correlation-based mapping between molecular states and pathways or phenotypes |
| Spatial Co-localization | entity(locᵢ) ↔ entity(loc) | Co-occurrence in spatial measurements without causal implication |
| Multi-omic Integration | CH + RNA → PW / C | Joint inference from multiple modalities |
