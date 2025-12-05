# AWM Internal Mechanics & Developer Notes (v0.2.0)

*Technical notes for developers, maintainers, and advanced users who want to understand AWM's internal behavior and constraints.*

## 1. Purpose of This Document

This document covers the **internal reasoning model**, **agent interactions**, **design constraints**, and **operational limitations** of AWM.

It is intended for builders, researchers, and designers who want to understand how the system actually operates inside the model runtime.

**It is not user-level documentation.**

It describes **how the system behaves**, not how to use it.

## 2. Internal Reasoning Model

AWM uses a **pipeline-of-prompts architecture**, not runtime code execution.

Each agent shapes the model's internal reasoning through:
- a static prompt specification
- a deterministic sequence
- a controlled handoff between stages

There is **no concurrency**, **no state mutation** beyond cached prompts, and **no persistent memory**.

**Key property:**

Each agent is a **shaping influence** on the LLM's hidden-chain-of-thought, not an independent actor.

Even though the conceptual model is "multi-agent," all execution happens inside the same reasoning engine.

## 3. Agent Interaction Model

Although agents are described as discrete roles, they operate through **sequential transformations** of the task:

```
Persona → Meta → Planner → Reviewer → Executor
```

### Persona
Sets initial stance and interpretation. It influences all subsequent steps by altering the latent framing.

### Meta
Normalizes contradictions and provides cross-agent guidance. Its effect is global but purely conceptual.

### Planner
Produces a structural decomposition.

This step is the **most strictly constrained**: AWM's determinism is anchored here.

### Reviewer
Simulates a "checking agent" by applying Reviewer's prompt rules to the Planner's structure.

If the structure violates constraints, the reviewer forces a "loop back" reconstruction.

This loop back is **entirely conceptual**; no files or state are mutated.

### Executor
The execution agent acts like a **renderer** that takes the Reviewer-approved plan and expands it into a deliverable.

### Knowledge
Not an agent in the pipeline; it is a **passive support layer**.

Injects or stabilizes definitions to maintain internal consistency.

## 4. Constraints & Enforcement Mechanisms

The **Reviewer is the enforcement point** of AWM.

It ensures:
- fidelity to the task envelope
- compliance with constraints
- alignment with required deliverable format
- elimination of contradictions

Internally, Reviewer operates by reinterpreting the Planner output through a stricter lens.

If violations exist:
- A revision cycle is triggered
- The conceptual plan is regenerated
- The system returns to Reviewer for final validation

No revisions happen to external files; the transformations are **purely reasoning-level**.

## 5. How AWM Uses the Schema Files

The system loads two key schemas:
- `task_protocol.json`
- `agents.json`

These do not execute or validate anything natively; instead, they inform the model about:
- what a valid task envelope must contain
- agent responsibilities
- logical requirements for deliverables

The model uses these schemas as **reference constraints** during reasoning.

In practice:
- **Planner** uses schema definitions to determine structure
- **Reviewer** uses schema definitions to validate structure
- **Executor** uses schema definitions to ensure compliance in final output

Schemas **anchor the system**; they are guardrails, not programs.

## 6. Knowledge Layer Mechanics

AWM's knowledge layer (glossary + memory files):
- does **not** update dynamically
- does **not** store user-specific state
- is **not** used as retrieval-augmented memory

Instead, these files act like:
- conceptual dictionaries
- persistent definitional resources
- fixed grounding for terms used across documents

**Important:**

Knowledge does not override user constraints.

It exists to **stabilize definitions**, not to drive output.

## 7. Execution Boundaries

AWM explicitly **cannot**:
- run code
- call APIs
- reference external systems after activation
- write files
- alter its loaded prompts
- update schemas
- modify the workflow
- persist any state beyond the conversation session

These boundaries are **design constraints**, not technical limitations.

They ensure:
- deterministic output
- safety
- explainability
- predictable behavior

## 8. Planner/Reviewer Loop Mechanics

The most important internal dynamic is the **plan-review loop**.

### Planner Phase
Generates a structured outline.

### Reviewer Phase
Checks:
- section completeness
- coverage of constraints
- consistency with task and schema
- internal logical flow

If Reviewer finds an issue:
- The system conceptualizes an internal correction
- Planner re-drafts the plan
- Reviewer re-checks

**Execution only happens once Reviewer is satisfied.**

This loop ensures:
- high internal coherence
- adherence to constraints
- deterministic structure

## 9. Determinism Model

AWM is **deterministic** in the sense that:

given the same:
- task
- constraints
- prompts
- system behavior

...then outputs will be **materially identical**.

This results from:
- strict ordering
- structured planning
- constraint enforcement
- minimal stylistic variability

**Creativity is secondary to structure and consistency.**

## 10. Developer Considerations

If modifying the AWM framework:

### Do NOT:
- change activation rules
- combine or merge agent roles
- introduce unstated behaviors
- allow Planner or Executor to bypass Reviewer
- create agent recursion beyond the P–R verification loop

### Safe areas to modify:
- prompt phrasing (while preserving responsibilities)
- glossary expansion
- domain-specific memory additions
- deliverable templates
- specialization schemas

### Unsafe areas:
- changing workflow sequence
- removing constraints
- enabling freeform generation
- adding runtime code-like logic

## 11. Rationale Behind the Architecture

The design goals of AWM:

### 1. Explainability
Each agent has one purpose and acts in a defined order.

### 2. Transparency
All behavior derives from visible prompts, not opaque heuristics.

### 3. Reliability
The Planner–Reviewer loop prevents sloppy or malformed output.

### 4. Bounded Creativity
Outputs must conform to user constraints and schema expectations.

### 5. No Autonomous Mutation
AWM cannot modify its own structure, ensuring safety and predictability.

## 12. Summary

AWM v0.2.0 is a deterministic, constraint-driven, multi-agent reasoning pipeline composed of:

- **Six conceptual agents**
- **Two schemas**
- **Two knowledge modules**
- **A strict workflow engine**
- **A controlled activation mechanism**

Internally, it operates via sequential shaping prompts, enforcing structure and correctness through a Planner–Reviewer loop before executing the final deliverable.

It is auditable, predictable, and intentionally non-autonomous.
