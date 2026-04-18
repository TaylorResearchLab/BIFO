# Section D: Admissibility Constraints

*BIFO Specification v0.02*

Admissibility constraints govern all transformations in a BIFO-conditioned graph.

| Constraint | Description |
|------------|-------------|
| **Directionality** | All flows follow biological causality with implicit time dependence. Each directed flow is a distinct biological process; the forward and reverse of a given transformation are represented as separate flows with independent admissibility conditions, enzymes, and energetic requirements. |
| **State dependence** | Flows depend on elements such as CH, RNA role, P(state), SM, ION, MECH, and location. |
| **Metabolic model consistency** | For analyses invoking energetic or metabolic state, BIFO requires inclusion of the relevant genes, enzymes, metabolites, reactions, and compartmental states needed to preserve consistency with an underlying genome-scale metabolic model or related biochemical network representation. |
| **Spatial constraints** | Processes restricted to valid compartments. Spatial admissibility is evaluated against the S entity class; flows are inadmissible if source and target entities occupy incompatible spatial contexts. |
| **Temporal progression** | All transitions follow state(t) → state(t+1). |
| **Non-flow exclusion** | Remove hierarchy edges (is_a, part_of), identifier/lexical edges (CODE, TERM, label), and purely statistical or topological relationships. These must not be treated as information-carrying flows. |
| **Contextual admissibility** | Flow validity may depend on local context (e.g., compartment, cell type, spatial region) rather than global system state. |

## Formal definition

Let G = (V, E) be the input property graph with node set V and edge set E, where each edge e = (u, v, p) carries predicate p and endpoint entity classes c_u, c_v. BIFO conditioning defines a constraint operator C that produces the conditioned propagation graph G_C = (V, E_C), where:

```
E_C = { e ∈ E : flow_class(predicate(e)) ∈ F_admissible
               ∧ entity_class(source(e)) resolved
               ∧ entity_class(target(e)) resolved }
```

F_admissible is the set of flow classes designated as propagating (mechanistic and weak-mechanistic tiers, plus Pathway Contribution bridge edges; excluding Observational Association, contextual_constraint, and nonpropagating_context tiers).

The operator is **graph-agnostic**: it depends only on the predicate-to-flow mapping and entity resolution rules, not on graph topology.
