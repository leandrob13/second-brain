# Universal Building Blocks of Knowledge Representation

While Artificial Intelligence has developed many different formalisms for representing knowledge (such as Logic, Rule-Based Systems, Semantic Networks, Frames, and Ontologies), they all rely on the same foundational concepts. 

If you strip away the specific syntax, code, and mathematics of these different systems, you find a shared set of "primitives"—the fundamental Lego bricks of Knowledge Representation. 

## 1. Instances (Individuals / Tokens)
Instances are the specific, concrete entities that actually exist in the real world (or the specific data domain). They are the granular data points a system reasons about.
- **Example:** `Albert_Einstein`, `My_Toyota_Camry`, `Order_#12345`.
- **How they appear:**
  - *Logic:* Constants or Objects.
  - *Semantic Networks:* The leaf nodes at the very bottom of the graph.
  - *Frames & Ontologies:* Individuals or Instances.
  - *Taxonomies:* The specific files at the end of a folder tree.

## 2. Classes (Concepts / Types)
Classes are the abstract categories or groups that instances belong to. They define the "what is it?" for any given instance. Organizing classes into a hierarchy is the entire purpose of a Taxonomy.
- **Example:** `Human`, `Vehicle`, `Transaction`.
- **How they appear:**
  - *Logic:* Unary Predicates or Sets (e.g., `Vehicle(x)` meaning x is a Vehicle).
  - *Semantic Networks:* The higher-level, broader nodes.
  - *Frames & Ontologies:* Classes.

## 3. Properties (Attributes / Relations / Slots)
Properties define *how* things are connected or what characteristics they have. They provide the lateral connections that turn a strict vertical taxonomy into a rich, interconnected web.
- **Example:** `hasColor`, `isEmployedBy`, `dateOfBirth`.
- **How they appear:**
  - *Logic:* Binary Predicates (e.g., `isEmployedBy(Albert, Patent_Office)`).
  - *Semantic Networks:* The directed edges or links connecting the nodes (like `HAS-A` or `OWNS`).
  - *Frames:* Slots (e.g., a slot for `Color` on a Car frame).
  - *Ontologies:* Properties (Object Properties connect two instances; Datatype Properties connect an instance to a raw value, like an integer or string).

## 4. Inheritance
If a Taxonomy provides the structural tree of classes, Inheritance is the *behavioral mechanism* that operates on that tree. It is the rule dictating that a child concept automatically receives the traits of its parent concept, which prevents massive data duplication.
- **Example:** Because a `Canary` is a subclass of `Bird`, it logically inherits the property `hasFeathers = True` without the system needing to explicitly record that fact for every single canary in the database.
- **How they appear:**
  - *Semantic Networks:* Tracing a path upward through `IS-A` links to infer facts.
  - *Frames:* The primary way a new object adopts its default values.
  - *Ontologies:* Subclasses logically adopting all the axioms and properties of their Superclasses.

## 5. Axioms (Constraints / Rules)
Axioms are absolute statements of truth or strict limits placed on the system. They define the boundaries of the domain and prevent the AI from making illegal, illogical inferences.
- **Example:** "A Person can have a maximum of two biological parents" or "A Vehicle cannot also be a Person" (disjoint classes).
- **How they appear:**
  - *Logic:* The core mathematical formulas that must resolve to True.
  - *Rule-Based Systems:* The "If-Then" condition-action rules.
  - *Frames:* Facets (e.g., a constraint that an `Age` slot must only accept positive integers).
  - *Ontologies:* Restrictions or Logical Axioms.

## Summary: Building Up Complexity
You can think of different Knowledge Representation techniques as just different combinations of these core blocks, scaled up in complexity:
- **Classes + Inheritance** = A *Taxonomy* (Great for organization)
- **Classes + Instances + Properties** = A *Semantic Network* / *Knowledge Graph* (Great for interconnected data)
- **Classes + Properties + Axioms** = An *Ontology* (Great for strict, shared meaning and machine reasoning)


## References & Further Reading
- Brachman, R. J. (1983). *What IS-A Is and Isn't: An Analysis of Taxonomic Links in Semantic Networks.* IEEE Computer.
- Sowa, J. F. (2000). *Knowledge Representation: Logical, Philosophical, and Computational Foundations.*
