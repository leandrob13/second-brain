# Semantic Networks

## Overview
Semantic Networks are a graphical approach to Knowledge Representation. They represent knowledge as a network (or graph) where the nodes represent concepts, objects, or situations, and the edges (links) represent the semantic relationships connecting them.

## Key Components

1. **Nodes (Vertices):** Represent entities, concepts, or instances in the domain.
   - *Examples:* `Bird`, `Wings`, `Penguin`, `Animal`.
2. **Edges (Links/Arcs):** Represent the directional relationships connecting nodes. Common types of links include:
   - `IS-A` (Inheritance): Defines taxonomy and classification (e.g., `Penguin` IS-A `Bird`).
   - `HAS-A` (Part-Whole): Defines properties and composition (e.g., `Bird` HAS-A `Wings`).
   - Action/State relations: Specific verbs connecting concepts (e.g., `Bird` CAN `Fly`).

## Visualizing Knowledge
Imagine a connected graph where:
- `Canary` --(IS-A)--> `Bird`
- `Bird` --(IS-A)--> `Animal`
- `Bird` --(HAS-A)--> `Feathers`

Through this structure, a system can naturally infer that a `Canary` has `Feathers` by tracing the path upward through the `IS-A` hierarchy. This core concept of **inheritance** allows the network to store data efficiently without repeating facts for every single instance.

## Modern Application: Knowledge Graphs
Semantic networks laid the conceptual groundwork for modern **Knowledge Graphs** (such as Google's Knowledge Graph, DBpedia, and enterprise data fabrics). Today, they are widely used in search engines and AI to represent complex relationships between billions of entities (people, places, organizations) on the internet.

## Advantages and Disadvantages
- **Pros:** Highly intuitive and easy for humans to visualize and understand. They provide a very natural representation of hierarchies, inheritance, and interrelated concepts.
- **Cons:** Historically, they lacked formal logical semantics (different developers might use the `IS-A` link to mean slightly different things). This structural ambiguity ultimately led to the development of more formal, rigorous structures like Ontologies.


## References & Further Reading
- Quillian, M. R. (1968). *Semantic Memory.* In M. Minsky (Ed.), Semantic Information Processing. MIT Press.
- Sowa, J. F. (1987). *Semantic Networks.* Encyclopedia of Artificial Intelligence.
