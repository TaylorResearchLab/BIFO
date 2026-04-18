# Section A: Entity Classes and State Definitions

*BIFO Specification v0.02*

Entity classes represent the major organizational layers of biology. Each entity carries an internal state appropriate to its biological role.

## Entity Classes

| Class | Description |
|-------|-------------|
| G | Genetic sequence and variation (genes, loci, variants) |
| CH | Chromatin state over genomic regions |
| RNA | Transcripts with role-dependent function |
| P | Proteins with state-dependent activity |
| CM | Macromolecular complexes and assemblies |
| SM | Small molecules with role-dependent semantics |
| ION | Ionic and electrochemical state |
| PW | Pathways and biological programs |
| C | Cellular configuration. May represent individual cells or aggregated cellular populations depending on modeling resolution. |
| S | Spatial context across scales |
| PH | Phenotype (observable traits) |
| DS | Disease (multi-phenotype construct) |
| X | External perturbations |
| MECH | Mechanical and physical state |

## State Definitions

| Entity | State Definition |
|--------|-----------------|
| CH | (accessibility, methylation, histone_modifications, 3D_topology) |
| RNA | (role ∈ {coding, regulatory, processing, structural}, abundance, stability, translation_state, processing_state, location) |
| P | (identity, PTM_state, activity, location) |
| CM | (composition, activity_state, location) |
| SM | (identity, role ∈ {energetic, metabolic, signaling, perturbational, environmental}, concentration, redox_state) |
| ION | (species, concentration, gradient, membrane_potential) |
| C | (chromatin_attractor, functional_state, cell_cycle_state) |
| S | (macro_anatomy, subcellular_compartment, extracellular_region, boundary_role) |
| MECH | (force, stiffness, tension, geometry) |
| X | (identity, mechanism ∈ {inhibitor, activator, degrader, modulator, environmental}, target_entity, concentration_range, evidence_source) |

## Notes

**Binding interactions** in BIFO are implicitly defined over sequence- or structure-specific substrates (e.g., genomic regions, RNA sequences, or protein interfaces), which determine admissibility of regulatory and signaling flows, including constraints imposed by spatial localization.

**Spatial context S** forms a hierarchical and connected topology across anatomical regions, subcellular compartments, and extracellular domains, enabling constraint of transport, processing, and signaling flows.

**Complex boundary criterion:** A macromolecular complex (CM) is instantiated when two or more protein entities associate with sufficient stability to execute a coordinated function as a unit, evidenced by co-purification, structural data, or functional assay. Transient interactions that do not meet this criterion are represented as direct state transformations between P entities without persistent CM instantiation. When directionality evidence is available (e.g., one protein acts as kinase and the other as substrate), a transient interaction is represented as a directed Signal Transduction flow P(state) → P(state') rather than a symmetric Protein Interaction.

## Formal System Representation

```
sᵥᵗ⁺¹ = Σᵤ∈N(v) Aᵣ(sᵤᵗ, sᵥᵗ, m, ℓ) · Tᵣ(sᵤᵗ, sᵥᵗ, m, ℓ)
```

Where:
- **Tᵣ** is the transformation operator for flow class r
- **m** represents optional metabolic or biochemical model context
- **ℓ** represents spatial context
- **Aᵣ** is the admissibility function

The admissibility function Aᵣ is evaluated against: (1) the BIFO entity class of the source node, (2) the BIFO entity class of the target node, (3) the BIFO flow class of the connecting edge, and (4) the current state of both nodes.

The system consists of an **informational state space** defined over G, CH, RNA, P, CM, PW, C, PH, and DS, and a **resource and physical state space** defined over SM, ION, and MECH. Informational flow drives system behavior, while resource and physical states constrain admissible transitions.

**Minimum graph requirements:** CURIEs on typed nodes and edges, and provenance of assertions.
