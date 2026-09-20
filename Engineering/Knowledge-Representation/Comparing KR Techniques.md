# Comparing Knowledge Representation Techniques

If Knowledge Representation as a whole is the science of *how* we model information, the individual techniques we use each focus on solving a different piece of the cognitive puzzle. 

If an **Ontology** focuses on the *"What"* (what exists and what it precisely means), how do the other techniques fit in? 

Here is how the five major Knowledge Representation formalisms compare, what specific problem they solve, and the cognitive "question" they answer.

## 1. Logic-Based Systems: The "True or False" (Proof)
- **What they solve:** The need for absolute mathematical certainty and rigorous proof. 
- **The Focus:** Deduction. Given a set of premises, what facts are unequivocally true without any contradictions?
- **Analogy:** The Mathematician. It doesn't care about intuition or context; it only cares about whether the equation balances perfectly according to strict rules.

## 2. Rule-Based Systems: The "If-Then" (Action)
- **What they solve:** The need to replicate human procedural decision-making and diagnostic processes.
- **The Focus:** Process. If a specific condition is met, what action should be taken or what immediate conclusion should be drawn?
- **Analogy:** The Doctor or the Mechanic. It uses a checklist of symptoms (facts) to arrive at a specific diagnosis (conclusion) or execute a procedure.

## 3. Semantic Networks: The "How It Connects" (Relationships)
- **What they solve:** The need to understand taxonomies, inheritance, and the graphical relationships between different entities.
- **The Focus:** Connection. How do concepts inherit properties from their parent categories? (e.g., inferring a Canary has feathers because it's linked to Bird).
- **Analogy:** The Mind Map. It provides a visual, intuitive web of how different ideas link together, prioritizing relationships over strict mathematical rules.

## 4. Frames: The "What to Expect" (Context & Defaults)
- **What they solve:** The need to handle stereotyped situations and missing information by grouping related attributes together.
- **The Focus:** Context. When you encounter a specific entity, what default properties and behaviors should you immediately assume it has?
- **Analogy:** The Blueprint or Stereotype. When you walk into a "Restaurant", you automatically expect a menu, a table, and a bill, filling in the blanks in your mind before you even see them.

## 5. Ontologies: The "What It Means" (Shared Vocabulary)
- **What they solve:** The need for standardized, unambiguous communication across different databases, software systems, and organizations.
- **The Focus:** Agreement. Defining a rigid schema of classes, properties, and axioms so that everyone (and every computer) agrees on exactly what a concept means.
- **Analogy:** The Dictionary & Grammar Book combined. It ensures that when System A says "Transaction," System B knows the exact constraints, properties, and meaning of that specific word.

## Summary Matrix

| Technique | The Cognitive Focus | Primary Strength | Primary Weakness |
| :--- | :--- | :--- | :--- |
| **Logic** | The "True/False" | Absolute precision and proof | Slow, struggles with ambiguity |
| **Rules** | The "If-Then" | Modular, replicable decision-making | Hard to maintain as rules conflict |
| **Semantic Nets**| The "Connection" | Intuitive hierarchies and inheritance | Historically lacked strict formal rules |
| **Frames** | The "Expectation" | Handles context and missing data well | Standalone systems are rare today |
| **Ontologies** | The "Meaning" | Shared, unambiguous standardization | Complex and time-consuming to build |

## How They Work Together
In modern AI, these techniques are rarely used in isolation. For example, a modern enterprise Knowledge Graph might use an **Ontology** to define the standard vocabulary, **Semantic Networks** to structure the massive web of data, and **Logic/Rule-Based Systems** acting as an inference engine on top of it to deduce new facts.


## References & Further Reading
- Sowa, J. F. (2000). *Knowledge Representation: Logical, Philosophical, and Computational Foundations.*
- Brachman, R. J., & Levesque, H. J. (2004). *Knowledge Representation and Reasoning.* Elsevier.
