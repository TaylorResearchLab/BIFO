# Section F: Confidence and Provenance

*BIFO Specification v0.02*

Each retained edge in a BIFO-conditioned graph carries confidence and provenance attributes that enable evidence-weighted inference and reproducibility.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **Confidence** | Strength or reliability of the relationship, expressed as a normalized score within source or as a categorical level (high / medium / low) |
| **Provenance** | Source dataset or ontology from which the edge was derived |
| **Evidence Type** | Experimental vs. inferred vs. curated vs. predicted |
| **Composite Confidence** | For multi-hop paths: minimum edge confidence along path, typed at the lowest evidence tier present |

## Evidence type hierarchy

```
experimental > curated > inferred > predicted
```

## Notes on implementation

Not all edges require the same level of confidence annotation. Well-established biological relationships (e.g., gene → RNA transcript via central dogma) may not require explicit confidence scoring, while state-dependent relationships (e.g., protein(state1) → protein(state2) via PTM signaling) benefit from evidence-level annotation that captures whether the specific state transition is experimentally validated, inferred from pathway databases, or predicted computationally.

Confidence scores from heterogeneous sources must not be compared directly. They should be normalized within source before composition into composite path confidence.

## Relationship to BIFO flow classes

Confidence annotation interacts with flow class assignment in two ways:

1. **Classification threshold:** When a predicate has mixed evidence quality across instances, BIFO applies the weaker classification (e.g., classifying as `weak_mechanistic` rather than `mechanistic` if some instances are correlational). This is a conservative design choice that prevents high-confidence mechanistic relationships from being diluted by observational instances of the same predicate type.

2. **Propagation weighting:** Observational edges (non-propagating) retained in the conditioned graph may be used to weight or constrain mechanistic propagation, with their confidence scores contributing to the weighting function. The specific weighting scheme is implementation-dependent.
