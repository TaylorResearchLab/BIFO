# DDKG-BIFO Implementation Guide

**Reference implementation for the Biological Information Flow Ontology**

**KG:** Data Distillery Knowledge Graph (DDKG), built on Unified Biomedical Knowledge Graph (UBKG) and Petagraph infrastructure  
**BIFO version:** v0.02  
**Mapping version:** v0.7.1 (`bifo_ddkg_mapping.yaml`)  
**Implementation pipeline:** https://github.com/TaylorResearchLab/bifo-graph

---

## Overview

This guide documents how the BIFO Graph Conditioning Protocol (Specification §E) is applied to the DDKG. It specifies: (1) the mapping from DDKG node types and identifiers to BIFO entity classes, (2) the mapping from DDKG edge predicates to BIFO flow classes, (3) which BIFO flow classes are fully, partially, or not instantiable in the current DDKG build, and (4) DDKG-specific extensions and restrictions to the normative CURIE mapping tables.

---

## Step 1: Entity class assignment in DDKG

### Node structure

DDKG nodes carry a source authority base (SAB) identifier that determines their vocabulary of origin. Entity class assignment uses a two-level procedure:

**Level 1 (preferred):** Match each concept node to a SAB by reading against UBKG Code nodes. In builds where Code nodes are present, this resolves nodes to their source vocabulary directly.

**Level 1 fallback:** Read the SAB column directly from the exported `nodes.csv`. This fallback resolved 18,897 of 34,523 concept nodes (54.7%) in the v1.0 benchmark export.

**Level 2:** Nodes that cannot be assigned a SAB are treated as unresolvable. Edges with at least one unresolvable endpoint are dropped.

### SAB-to-entity-class mapping (DDKG-specific)

| SAB | BIFO Entity Class | Notes |
|-----|-------------------|-------|
| HGNC | G | Gene nodes — primary gene entity |
| ENSEMBL | G | Ensembl gene IDs |
| REFSEQ | G | RefSeq gene/transcript |
| CLINVAR | G (variant) | Variant entities |
| DBSNP | G (variant) | dbSNP variant IDs |
| GNOMAD | G (variant) | Population variant frequencies |
| KFPT | G (variant) | Kids First variant burden |
| KFGENEBIN | G (variant) | Kids First gene-level burden bins |
| ENCODE.CCRE | CH | ENCODE cis-regulatory elements |
| ENCODE.CCRE.ACTIVITY | CH | ENCODE element activity states |
| ENCODE.CCRE.CTCF | CH | CTCF binding elements |
| ENCODE.CCRE.H3K27AC | CH | H3K27Ac histone marks |
| ENCODE.CCRE.H3K4ME3 | CH | H3K4Me3 histone marks |
| ENCODE.RBS.150.NO.OVERLAP | CH | ENCODE regulatory binding sites |
| 4DNL | CH | 4DN chromatin loop annotations |
| GTEXEXP | RNA | GTEx expression — RNA abundance |
| UNIPROTKB | P | UniProtKB protein entities |
| PUBCHEM | SM | PubChem compound IDs |
| CHEBI | SM | ChEBI chemical entities |
| DRUGBANK | SM | DrugBank drug entities |
| MSIGDB | PW | MSigDB gene set pathways |
| WP | PW | WikiPathways |
| GO | PW / CH / S | GO biological process (PW), cellular component (S/CH) |
| REACTOME | PW | Reactome pathways |
| HP | PH | Human Phenotype Ontology |
| OMIM | DS | OMIM disease entries |
| MONDO | DS | MONDO disease ontology |
| MEDGEN | DS | NCBI MedGen |
| ORPHANET / ORDO | DS | Orphanet rare diseases |
| DGNBM, DGNCM, DGNGCM, DGNGDA, DGNGV, DGNAGE | — | DisGeNET provenance records (opaque MD5 hash IDs — excluded; biological content resolves via HGNC/MONDO/HPO) |

### Coverage in v1.0 benchmark

- Total concept nodes: 34,523
- Resolved to BIFO entity class: 18,897 (54.7%)
- Unresolvable (no SAB match): 15,626 (45.3%)
- Unresolved nodes are structurally peripheral; their edges are dropped rather than admitted with unknown classification

The 45.3% unresolved fraction reflects the 1-hop export design: the export deliberately includes a broad neighborhood, but many peripheral nodes belong to vocabularies not represented in the current SAB selection.

---

## Step 2: Flow class assignment in DDKG

### Mapping file

The complete predicate-to-flow-class mapping is encoded in `bifo_ddkg_mapping.yaml` (v0.7.1), maintained in the `bifo-graph` pipeline repository at `config/bifo_ddkg_mapping.yaml`.

The mapping contains:
- **251** predicate-to-flow class entries
- **96** explicit non-flow (excluded) predicate designations
- **46** observational edge definitions
- **5** classification tiers

### Classification tiers

| Tier | Description |
|------|-------------|
| `mechanistic` | Direct, causal biochemical or molecular events with clear directionality |
| `weak_mechanistic_or_observational` | Relationships that may reflect mechanism but whose evidence is mixed or correlational |
| `observational` | Statistical associations without mechanistic grounding |
| `contextual_constraint` | Spatial or temporal constraints that modify but do not propagate signal |
| `nonpropagating_context` | Structural relationships excluded from PPR operator but retained in conditioning output |

### Classification philosophy

The classification is deliberately conservative: when a predicate type has mixed evidence quality across instances, the weaker classification applies. This prevents high-confidence mechanistic relationships from being diluted by the presence of observational instances of the same predicate type in the same graph.

### Flow class instantiation in current DDKG build

| BIFO Flow Class | Status | DDKG predicates | Edge count (benchmark) |
|----------------|--------|-----------------|----------------------|
| Signal Transduction | Fully instantiated | 10 predicates | 5,786 |
| Transcription | Fully instantiated | 9 predicates | 1,568 |
| Translation | Fully instantiated | 9 predicates | — |
| Protein Interaction | Fully instantiated | 20 predicates | — |
| Signal Termination | Fully instantiated | 4 predicates | 484 |
| Perturbational Effect | Fully instantiated | 14 predicates (mechanistic + weak-mech subsets) | 5,392 |
| Genetic Regulatory Modulation | Partially instantiated | 11 predicates (64 non-propagating weak-mech edges) | — |
| Biochemical Transformation | Fully instantiated | 24 predicates | — |
| Pathway Contribution | Fully instantiated | 27 predicates | 80,200 |
| Observational Association | Retained non-propagating | 41 predicates | 9,909 |
| Spatial constraint | Retained non-propagating | 47 predicates | 60 |
| State Progression | Fully instantiated | 19 predicates | — |

### Special cases

**Pathway Contribution edges** encode curated gene-to-pathway membership relationships. They do not represent direct biochemical interactions; they are admissible bridge edges between molecular entities and pathway-level biological programs. Their admission as a propagation-eligible flow class is what enables signal transfer from the mechanistic gene neighborhood to the pathway annotation layer — a structural property of ontology-aligned knowledge graphs.

**Perturbational Effect** contains two distinct predicate groups: direct molecular interactions (classified `mechanistic`) and correlation-derived associations (classified `weak_mechanistic_or_observational`). These are classified at the predicate level within the same flow class.

**GTEx coexpression** edges are handled in Python by checking whether the predicate string contains `coexpression`. All 108 tissue-specific `coexpression_*` and `inverse_coexpression_*` predicates match this pattern and are classified as Observational Association (non-propagating).

---

## Steps 3–4: State/spatial annotation and provenance

In the current DDKG implementation, full state annotation (Step 3) and confidence propagation (Step 4) are not yet implemented. The conditioning pipeline operates on predicate-to-flow-class assignment and entity resolution only. State-dependent admissibility and edge-level confidence weighting are planned for future versions.

---

## Conditioning results (v1.0 benchmark, run V5)

| Metric | Value |
|--------|-------|
| Input edges (merged) | 174,352 |
| Kept after conditioning | 104,342 (59.8%) |
| Propagating edges (full arm) | 94,309 |
| Non-propagating retained | 10,033 |
| Dropped: unmapped predicate | 37,448 (21.5%) |
| Dropped: unresolved entity | 32,214 (18.5%) |
| Dropped: non-flow classification | 348 (0.2%) |

---

## Pipeline scripts

The DDKG-BIFO conditioning pipeline is implemented in three Python scripts in `bifo-graph/pipeline/`:

- `bifo_conditioning.py` — entity resolution, flow class assignment, PPR propagation
- `score_pathways.py` — pathway scoring from conditioned score vectors
- `baseline_enrichment.py` — enrichment baseline methods

See the [bifo-graph repository](https://github.com/TaylorResearchLab/bifo-graph) for full documentation.
