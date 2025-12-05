# AWM User Guide (v0.2.0)

*Practical guide for users interacting with the Autonomous Workflow Model*

## 1. Purpose of This Guide

This document teaches you how to use AWM v0.2.0 once it is activated.

It is not an architectural description—it is operational guidance for issuing tasks, understanding outputs, and working with the multi-agent workflow.

## 2. Activation Requirements

AWM must be activated before it can accept tasks.

Activation requires:

- Loading all required AWM files via separate web.run open calls
- Caching the raw text contents exactly as delivered
- Confirming initialization with: "AWM v0.2.0 fully loaded."

Once activation is confirmed, you may issue AWM tasks using the directive:

```
awm_task: <task description>
```

## 3. The Task Envelope

A valid AWM task must follow the structure defined in task_protocol.json.

**Minimum components:**
- task description — what you want done
- constraints — what must or must not appear
- deliverables — format, sections, or specific output
- context — optional, adds domain info

**Example minimal task envelope:**
```
awm_task: create a risk assessment for a small IT business
```

**Expanded envelope example:**
```json
{
  "task_id": "risk-assessment-it-smallbiz",
  "task": "Create a structured risk assessment for a small IT business.",
  "constraints": {
    "style": "concise",
    "ethics_required": true
  },
  "deliverables": {
    "type": "structured_document",
    "required_sections": [
      "introduction",
      "risk_categories",
      "analysis",
      "recommendations"
    ]
  },
  "context": {
    "industry": "managed IT services"
  }
}
```

AWM tolerates shorthand, but full envelopes produce more stable results.

## 4. What Happens When You Issue an AWM Task

Once you send `awm_task: ...`, the following internal sequence occurs:

### 1. Persona Agent
Establishes stance, tone, and interpretation of your task.

### 2. Meta Agent
Ensures conceptual clarity and alignment across the entire workflow.

### 3. Planner Agent
Generates a structural, section-based plan for the deliverable.
Specifies headings, subsections, steps, and expected content.

### 4. Reviewer Agent
Checks the plan for:
- adherence to constraints
- correctness
- completeness
- ethical boundaries

If issues are found:
→ Reviewer sends corrections back to Planner (internal loop).

### 5. Executor Agent
Produces the final deliverable strictly following the approved plan.

### 6. Knowledge Agent
Adds glossary-backed definitions if necessary.

## 5. How to Control Output Quality

AWM responds strongly to constraints.

**Useful constraint types include:**

### Style constraints
- "concise"
- "technical"
- "plain_language"
- "formal"

### Structural constraints
- required section lists
- headings
- bullet list limits
- maximum or minimum depth

### Ethical or domain constraints
- "ethics_required": true
- "avoid_overtechnical_detail": true

### Formatting constraints
- "type": "structured_plan"
- "type": "analysis_document"

The AWM Reviewer will enforce these strictly.

## 6. How to Request Multiple Documents

If you want more than one output, specify it in the envelope:

**Example:**
```
awm_task: produce a three-document set describing the project
```

Or in JSON:
```json
"deliverables": {
  "type": "multi_document",
  "count": 3,
  "titles": ["overview", "system design", "user guide"]
}
```

The Planner will split the output accordingly.

## 7. Handling Clarification Cycles

If AWM cannot proceed due to ambiguity, it will ask you a single, focused clarifying question.

AWM does not guess; it blocks until constraints are satisfied.

You must respond with the missing detail before execution continues.

## 8. What AWM Cannot Do

AWM intentionally cannot:

- Execute code
- Access external systems after activation
- Update its own knowledge files
- Store long-term state
- Take actions outside document generation
- Change its own architecture

It is a strictly internal reasoning framework.

## 9. Troubleshooting Common Issues

### Issue: AWM says it isn't loaded
**Likely cause:** one or more files were not successfully fetched during activation.

**Fix:** re-run activation exactly with all URLs.

### Issue: AWM rejects a task
**Common causes:**
- Missing deliverable type
- Ambiguous scope
- Conflicting constraints

**Fix:** provide one additional clarifying detail.

### Issue: Output seems too short or shallow
Add a constraint:
- "depth": "expanded"

Or specify:
- "include detailed subsections"

### Issue: AWM output deviates from your expected style
Add explicit style constraints ("concise", "hybrid", "formal", etc.).

## 10. Summary for Users

To use AWM effectively:

1. Activate the system via official URLs
2. Send tasks via `awm_task:`
3. Include constraints for style, structure, and deliverables
4. Respond to clarification questions when asked
5. Expect structured, predictable, deterministic outputs
