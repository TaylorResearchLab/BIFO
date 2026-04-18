# BIFO — Biological Information Flow Ontology

**Version:** v0.02  
**Status:** Active development  
**Maintained by:** Taylor Research Lab, Children's Hospital of Philadelphia / University of Pennsylvania

---

BIFO is a propagation theory for heterogeneous biomedical knowledge graphs. It defines the channel conditions under which biologically valid information flow occurs, independently of any specific knowledge graph implementation.

Analogously to how Shannon's information theory defines channel capacity independently of the physical transmission medium, BIFO defines admissible biological information flow independently of the underlying graph schema, identifier system, or database. The asymmetry introduced by admissible directional flows over a property graph constrains traversal to semantically valid biological paths, adding information by restricting the reachable state space.

BIFO addresses a gap not covered by existing frameworks:
- Ontologies (GO, HPO) provide semantic representations but do not define propagation rules
- Standards (SBML) encode mechanistic models but are not graph-agnostic
- Causal assertion frameworks (GO-CAM, BEL) describe relationships but do not generalize to heterogeneous graphs
- Graph ML approaches learn propagation from data rather than enforcing biologically interpretable constraints

**BIFO defines which transformations are biologically admissible and how information is allowed to propagate across heterogeneous knowledge graphs.**

---

## Repository structure

```
bifo/
├── spec/
│   └── BIFO_Specification_v0.02.pdf    Normative specification document
├── docs/
│   ├── entity_classes.md               Section A: Entity classes and state definitions
│   ├── curie_mapping.md                Section B: CURIE-to-entity and predicate-to-flow mapping
│   ├── flow_classes.md                 Section C: Flow class definitions and examples
│   ├── admissibility.md                Section D: Admissibility constraints
│   ├── conditioning_protocol.md        Section E: Four-step graph conditioning protocol
│   └── confidence_provenance.md        Section F: Confidence and provenance annotation
├── implementations/
│   └── ddkg/
│       └── IMPLEMENTATION_GUIDE.md     DDKG-BIFO implementation guide (reference implementation)
├── CHANGELOG.md                        Version history
└── LICENSE
```

---

## Quick reference

### Entity classes

| Class | Description |
|-------|-------------|
| G | Genetic sequence and variation |
| CH | Chromatin state |
| RNA | Transcripts |
| P | Proteins |
| CM | Macromolecular complexes |
| SM | Small molecules |
| ION | Ionic and electrochemical state |
| PW | Pathways and biological programs |
| C | Cellular configuration |
| S | Spatial context |
| PH | Phenotype |
| DS | Disease |
| X | External perturbations |
| MECH | Mechanical and physical state |

### Primary information flow backbone

```
G + CH → RNA → P(state) → PW → C → PH → DS
```

### Graph conditioning protocol (four steps)

1. **Entity class assignment** — assign BIFO entity class to each node
2. **Flow class assignment and edge filtering** — assign flow class to each edge; remove non-admissible edges
3. **State and spatial annotation** — annotate nodes/edges with state and context
4. **Confidence and provenance annotation** — annotate retained edges with evidence metadata

See [`docs/conditioning_protocol.md`](docs/conditioning_protocol.md) for full protocol.

---

## Compatibility

Any property graph satisfying the following is BIFO-compatible:
- Nodes represent biological entities carrying typed internal state
- Edges carry sufficient semantic information to be assigned to BIFO flow classes
- The graph supports provenance metadata sufficient to evaluate evidence type

BIFO does not require bidirectional edges, UMLS CUI structure, or SAB-tagged provenance. A graph satisfying the requirements partially is BIFO-compatible for the subset of flow classes whose requirements are met.

---

## Implementations

The **DDKG-BIFO Implementation Guide** (`implementations/ddkg/IMPLEMENTATION_GUIDE.md`) documents how the BIFO Graph Conditioning Protocol is applied to the Data Distillery Knowledge Graph (DDKG), built on the Unified Biomedical Knowledge Graph (UBKG) and Petagraph infrastructure. This is the reference implementation.

The analysis pipeline applying BIFO to the DDKG is maintained at:  
**https://github.com/TaylorResearchLab/bifo-graph**

---

## Citation

If you use BIFO, please cite:

```bibtex
@article{taylor2026bifo,
  title   = {{BIFO}: A Biological Information Flow Ontology for
             Knowledge Graph-Directed Pathway Analysis of Rare Variant Cohort Data},
  author  = {Taylor, Deanne M. and Mohseni Ahooyi, Taha and Stear, Benjamin
             and Zhang, Yuanchao and Lahiri, Aditya and Simmons, Alan
             and Callahan, Tiffany J. and Silverstein, Jonathan C.},
  journal = {Manuscript in preparation},
  year    = {2026}
}
```

---

## License

Specification: CC BY 4.0  
Code and tooling: MIT License
