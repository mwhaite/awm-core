# AWM Architecture (v0.2.0)

*Structural, functional, and operational architecture of the Autonomous Workflow Model*

## 1. System Architecture Overview

AWM is a **multi-agent sequential reasoning architecture** composed of:

- **Agent layer** (six cooperating prompt-defined roles)
- **Workflow layer** (ordered reasoning pipeline)
- **Schema layer** (protocols defining task and agent structure)
- **Knowledge layer** (glossary + memory)
- **Activation layer** (file loading, caching, and initialization)

The system is declarative: **it defines behavior but performs no external execution.**

## 2. Component Architecture

### 2.1 Agent Layer

AWM agents are not processes; they are **prompt roles** that shape how reasoning proceeds. Each is defined by a dedicated file loaded at activation.

#### Persona

- Establishes identity, tone, expertise, and behavioral constraints
- Frames the task context before any reasoning begins

#### Meta

- Ensures global consistency
- Reconciles contradictions or missing context
- Oversees cross-agent coherence

#### Planner

- Expands a task into a structured plan
- Defines deliverable sections, steps, and structural requirements
- Explicit, hierarchical decomposition

#### Reviewer

- Evaluates the plan for:
  - alignment with task constraints
  - logical consistency
  - structural completeness
  - ethical boundaries (if given)
- Can request adjustments before execution

#### Executor

- Produces the final deliverable following the reviewed plan
- Mimics deterministic implementation of the Planner’s structure

#### Knowledge

- Supplies glossary terms and contextual information
- Stabilizes definitions across tasks
- Does not learn; remains static within the session

## 3. Workflow Architecture

AWM operates as a **strict linear pipeline**:

```
Persona → Meta → Planner → Reviewer → Executor → Knowledge (as needed)
```

### 3.1 Persona Stage

- Interprets the user’s task envelope
- Establishes stance, voice, constraints
- Normalizes ambiguous or missing elements

### 3.2 Meta Stage

- Evaluates conceptual consistency
- Clarifies intent internally
- Adjusts interpretation without altering user instruction

### 3.3 Planner Stage

- Generates a structured blueprint:
  - sections
  - sub-sections
  - steps
  - logical flow
  - constraints and dependencies

### 3.4 Reviewer Stage

- Validates against the schema in task_protocol.json
- Ensures the plan satisfies:
  - deliverable type
  - required structural components
  - explicit constraints
- If needed, Reviewer loops back to Planner

### 3.5 Executor Stage

- Uses the approved plan to generate the final output
- No improvisation or deviation beyond plan structure

### 3.6 Knowledge Integration

- Supplementary reference layer
- Injects definitions where doing so increases clarity
- Never overrides user constraints

## 4. Schema Architecture

Two governing schemas define AWM’s operational correctness.

### 4.1 task_protocol.json

Defines how a valid AWM task envelope must be structured:

- `task_id`
- `task` description
- `constraints`
- `deliverables` (type, required sections, etc.)
- `context`

This schema prevents malformed tasks from entering the workflow.

### 4.2 agents.json

Defines each agent’s identity and responsibilities:

- name
- description
- role in pipeline
- capabilities
- dependencies

It provides the conceptual contract governing agent behavior.

## 5. Knowledge Layer Architecture

### glossary.json

- Contains canonical, authoritative definitions
- Ensures terminology consistency across outputs

### memory.json

- Provides contextual or domain knowledge used for refinement
- Static for the session; not updated dynamically

Knowledge is supplemental and does not override the Planner or Reviewer.

## 6. Activation Architecture

Before AWM can run:

1. All raw text files must be fetched via `web.run` open calls
2. Their contents must be cached exactly as delivered
3. No base64 decoding or transformations (for raw GitHub URLs)
4. The system confirms successful initialization

Activation is all-or-nothing: **if a file fails to load, AWM is not considered initialized.**

## 7. Architectural Design Philosophy

AWM architecture emphasizes:

### Deterministic multi-agent flow

- Predictable and repeatable reasoning patterns

### Strict structure-first design

- Planning is separated from execution

### Constraint-based output generation

- Ensures reliability and coherence

### Transparency and traceability

- Every stage corresponds to a specific role and responsibility

### Non-executing safety

- The system cannot run code or perform external actions
- It can only structure reasoning

## 8. Summary

The AWM v0.2.0 architecture is composed of:

- **6 Agents**
- **2 Schemas**
- **2 Knowledge modules**
- **A Workflow engine**
- **An activation mechanism**

Together they form a predictable, structured, multi-stage reasoning framework for generating coherent, constraint-driven deliverables.
