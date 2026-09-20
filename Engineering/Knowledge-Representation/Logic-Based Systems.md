# Logic-Based Systems

## Overview
Logic-based systems are among the oldest and most formal approaches to Knowledge Representation. They use strict mathematical logic to represent knowledge and rely on formal inference mechanisms (like logical deduction) to draw conclusions. The idea is to represent facts about the world as logical sentences and use automated theorem provers to infer new facts.

## Key Formalisms

### 1. Propositional Logic
The simplest form of logic where statements (propositions) are either entirely true or entirely false. 
- **Example:** `P` (It is raining), `Q` (The street is wet). 
- **Rule:** `P => Q` (If it is raining, then the street is wet).
- **Limitation:** It lacks expressiveness because it cannot represent relationships between specific objects or deal with variables.

### 2. First-Order Logic (FOL)
Also known as Predicate Logic, FOL is much more expressive. It introduces objects, predicates (properties or relations), variables, and quantifiers (Universal: ∀ "for all", Existential: ∃ "there exists").
- **Example:** 
  - Rule: `∀x (Man(x) => Mortal(x))` (All men are mortal). 
  - Fact: `Man(Socrates)`. 
  - Inference: Therefore, `Mortal(Socrates)`.

## Advantages
- **Precision:** Logic is highly rigorous, leaving no room for ambiguity in how knowledge is stated.
- **Soundness and Completeness:** Deductive inference guarantees that if the premises are true, the conclusions drawn by the system are unequivocally true.

## Disadvantages
- **Computational Complexity:** Solving complex logical proofs can be extremely slow and sometimes computationally intractable for large datasets.
- **Handling Uncertainty:** Pure logic struggles with ambiguity, partial truths, or probabilities (though mathematical variants like Fuzzy Logic and Probabilistic Logic attempt to address this).


## References & Further Reading
- Nilsson, N. J. (1991). *Logic and Artificial Intelligence.* Artificial Intelligence Journal.
- Russell, S., & Norvig, P. (2020). *Artificial Intelligence: A Modern Approach (Chapters 7-9).* Pearson.
