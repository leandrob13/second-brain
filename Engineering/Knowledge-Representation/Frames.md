# Frames

## Overview
Proposed by AI pioneer Marvin Minsky in 1974, a Frame is a data structure designed to represent a stereotyped situation or a specific entity. Frames organize knowledge into hierarchical structures that closely resemble the "classes" and "objects" used in modern Object-Oriented Programming (OOP).

## The Core Concept
When humans encounter a familiar situation (e.g., walking into a restaurant or a classroom), we pull up a mental "frame" of reference for that situation. This frame contains expectations: in a restaurant, there will be tables, menus, waiters, and a bill. If some information is missing, we fill it in with default assumptions based on the frame.

In AI, a Frame is a digital object-like structure that holds attributes and default values to replicate this cognitive process.

## Key Components of a Frame

A Frame consists of a name and a set of **Slots** (which act like attributes or properties). Each slot can have **Facets** (rules, defaults, or values associated with the slot).

- **Frame Name:** `Restaurant_Visit`
- **Slots:**
  - `Customer:` (Empty, waiting to be filled with an instance)
  - `Food:` (Restricted: Must be an item from the menu)
  - `Payment:` (Default value: Credit Card)
  - `Action:` (Procedure to calculate the bill and tip)

### Procedural Attachment (Demons)
A major innovation of Frames was allowing procedures (executable code) to be attached directly to slots. These are sometimes called "demons":
- **IF-NEEDED:** A procedure triggered dynamically when a value is requested but not present.
- **IF-ADDED:** A procedure triggered when a new value is inserted into a slot (similar to database triggers).

## Legacy and Impact
Frames were highly influential in bridging the gap between declarative knowledge (static facts) and procedural knowledge (executable actions). 

While standalone frame-based AI representation systems are relatively rare today, their conceptual DNA is everywhere. They directly inspired the object-oriented programming paradigms (classes, inheritance, properties, methods) used in almost every modern language like Java, Python, and C++.


## References & Further Reading
- Minsky, M. (1974). *A Framework for Representing Knowledge.* MIT-AI Laboratory Memo 306.
- Bobrow, D. G., & Winograd, T. (1977). *An Overview of KRL, a Knowledge Representation Language.* Cognitive Science.
