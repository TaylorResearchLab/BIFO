# Section B: CURIE Mapping Layer

*BIFO Specification v0.02*

BIFO provides a two-level CURIE mapping architecture that enables KG-agnostic operation. Any knowledge graph whose nodes and edges carry resolvable CURIEs from standard biomedical ontologies can be BIFO-conditioned through identifier resolution, without requiring schema-specific mappings.

BIFO compatibility can be computed as the fraction of nodes and edges in a given KG that successfully resolve through these mapping tables.

---

## B.1 Level 1: CURIE-to-Entity-Class Mapping

This mapping is normative and KG-agnostic.

| BIFO Entity Class | Ontology / Namespace | Example CURIEs |
|-------------------|---------------------|----------------|
| G | HGNC, Ensembl gene (ENSG), NCBI Gene, Sequence Ontology gene features | HGNC:11998, ENSG00000141510, SO:0000704 |
| G (variant) | ClinVar, dbSNP, gnomAD, Sequence Ontology variant types | rs12345, SO:0001060 |
| CH | Sequence Ontology chromatin features, ENCODE terms, 4DN ontology | SO:0001747, histone modification terms |
| RNA | Ensembl transcript (ENST), RNAcentral, Sequence Ontology transcript features | ENST00000269305, SO:0000233 |
| P | UniProtKB, Ensembl protein (ENSP), PRO ontology | P04637, ENSP00000269305, PR:000000001 |
| CM | GO cellular component complexes, Complex Portal, IntAct complex | GO:0005667, CPX-123 |
| SM | ChEBI, PubChem, HMDB, KEGG Compound | CHEBI:15422, CID:5793, HMDB0000122 |
| ION | ChEBI ionic species, GO ion transport terms | CHEBI:29101 (sodium ion) |
| PW | Reactome, GO biological process, KEGG Pathway, WikiPathways | R-HSA-212436, GO:0006096, hsa00010 |
| C | Cell Ontology, Azimuth cell types, CL terms | CL:0000057, CL:0000236 |
| S | UBERON, FMA, GO cellular component (compartment) | UBERON:0002107, GO:0005737 |
| PH | HPO, OMIM phenotype, MP (mammalian phenotype) | HP:0001636, MP:0010403 |
| DS | MONDO, DOID, OMIM disease, Orphanet | MONDO:0004975, DOID:14566 |
| X | PubChem (drugs/compounds), DrugBank, ChEBI perturbational, LINCS perturbagen IDs | PUBCHEM:2247, DB00945 |
| MECH | GO mechanical stimulus terms, SPARC ontology terms | GO:0009612 |

---

## B.2 Level 2: Predicate-to-Flow-Class Mapping

The following Relations Ontology (RO) terms and common biomedical predicates map to BIFO flow classes. This mapping is normative.

Where the same predicate maps to different flow classes depending on entity type, the entity-type-dependent rule takes precedence. When the same predicate connects entity types matching multiple flow classes, the more specific flow class (lower in the flow taxonomy hierarchy) takes precedence.

| Predicate / RO Term | BIFO Flow Class | Direction | Entity-Type Condition |
|---------------------|----------------|-----------|----------------------|
| RO:0002292 (expressed in) | Transcription | G + CH → RNA | Subject: G or CH; Object: S or RNA |
| RO:0002434 (interacts with) | Protein Interaction | P ↔ P | Both entities: P |
| RO:0002213 (positively regulates) | Signal Transduction or Pathway Contribution | P → P' or P → PW | Depends on object entity class |
| RO:0002212 (negatively regulates) | Signal Termination or Negative Feedback | P → P₀ or downstream → upstream | Depends on object entity class |
| RO:0002233 (has input) | Biochemical Transformation or Metabolic Coupling | SM → SM or P ↔ SM | Reaction context |
| RO:0002234 (has output) | Biochemical Transformation | SM → SM | Reaction context |
| RO:0002411 (causally upstream of) | Any directed flow | Source → Target | Resolved by entity class |
| RO:0002304 (causally upstream of, positive effect) | Signal Transduction or Transcription | Source → Target | Resolved by entity class |
| BFO:0000066 (occurs in) | Spatial constraint (admissibility) | — | Contributes to S context |
| RO:0001025 (located in) | Intracellular Transport or Spatial constraint | entity(locᵢ) → entity(loc) | Resolved by context |
| RO:0002430 (involved in) | Pathway Contribution | P or CM → PW | — |
| RO:0002092 (happens during) | Cell Cycle Constraint | cell_cycle_state → system state | — |
| regulates transcription (custom/GO) | Protein-DNA Regulation | P or CM + CH → RNA | Subject: P or CM |
| positively_regulated_by (LINCS) | Perturbational Effect | SM(perturbational) → RNA / P | Subject: SM or X |
| negatively_regulated_by (LINCS) | Perturbational Effect | SM(perturbational) → RNA / P | Subject: SM or X |
| molecularly_interacts_with | RNA-RNA Binding or Protein Interaction | Resolved by entity class | — |
| located_in (genomic, HSCLO-type) | Spatial constraint / Chromatin Topology | CH → S | Genomic context |

**Note:** Implementation guides for specific KGs should document any predicate mappings that deviate from or extend this normative table.
