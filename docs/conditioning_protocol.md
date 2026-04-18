# Section E: BIFO Graph Conditioning Protocol

*BIFO Specification v0.02*

The following four-step protocol converts a raw property graph into a BIFO-conditioned graph. This protocol is KG-agnostic and applies to any graph satisfying the requirements in the Graph Substrate Assumption. KG-specific implementation guides document how each step is performed for a given graph.

---

## Step 1: Entity Class Assignment

Assign each node a BIFO entity class from `{G, CH, RNA, P, CM, SM, ION, PW, C, S, PH, DS, X, MECH}` using the CURIE-to-entity-class mapping ([Section B.1](curie_mapping.md)) or direct type assignment if the KG carries typed nodes.

- Nodes that cannot be assigned to any BIFO entity class are excluded from the conditioned graph
- Record coverage as the fraction of nodes successfully assigned

---

## Step 2: Flow Class Assignment and Edge Filtering

For each edge, attempt assignment to a BIFO flow class using the predicate-to-flow-class mapping ([Section B.2](curie_mapping.md)). Apply the following filters, **removing** edges that are:

- **Hierarchy edges:** is_a, part_of, subClassOf, and equivalent structural relationships
- **Identifier and lexical edges:** CODE, TERM, label, synonym, cross-reference, and related annotation links
- **Purely statistical relationships:** correlation edges, co-expression edges without directional evidence, topological proximity scores
- **Inadmissible entity-class pairs:** edges connecting node pairs whose entity class combination does not match any admissible flow in the BIFO flow taxonomy

Edges that map to observational flow classes ([Section C](flow_classes.md) observational table) are **retained** in the conditioned graph but **flagged as non-propagating**. They may inform weighting or constraint evaluation but must not carry biological state in forward propagation.

---

## Step 3: State and Spatial Annotation

Annotate nodes and edges with state and context information required for admissibility evaluation:

- Attach relevant state components to each node per [Section A Table 2](entity_classes.md) (CH state, RNA role, P(state), SM role, X mechanism, etc.) from available KG properties or ontology-derived defaults
- Assign spatial context (S entity class) to entities where location information is available, using the CURIE-to-entity-class mapping for GO cellular component, UBERON, or equivalent terms
- For flows involving the CM boundary criterion, apply the complex/transient classification rule defined in [Section A](entity_classes.md)

---

## Step 4: Confidence and Provenance Annotation

Annotate each retained edge with confidence and provenance attributes per [Section F](confidence_provenance.md). Apply the following confidence propagation rule for composite multi-hop flows:

**Confidence propagation rule:** The confidence of a composite flow path is the minimum edge confidence along the path (weakest-link principle), weighted by evidence type.

Evidence type ordering:
```
experimental > curated > inferred > predicted
```

When edges of mixed evidence type appear in a path, the composite evidence type is the lowest-ranked type present. Confidence scores from heterogeneous sources must not be compared directly; they should be normalized within source before composition.

---

## Coverage reporting

After conditioning, report:
- Fraction of nodes successfully assigned to a BIFO entity class (Step 1 coverage)
- Fraction of input edges retained as admissible (Step 2 coverage)
- Fraction of retained edges that are propagating vs. non-propagating (observational)
- Breakdown of dropped edges by cause: unresolved entity, unmapped predicate, non-flow classification

See the DDKG implementation guide for example coverage statistics from the reference implementation.
