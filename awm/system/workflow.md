# AWM Workflow (v0.2.0)

## 1. Normal usage (`awm_task`)

1. You provide an envelope like:

```jsonc
{
  "task_type": "awm_task",
  "task": "Design a simple makerspace co-op structure for Merced.",
  "context": "Audience: technically competent adults. Focus on practicality.",
  "inputs": {},
  "constraints": { "style": "concise, implementation-focused" },
  "output_format": "markdown",
  "success_criteria": [
    "Roles and responsibilities clear",
    "Decision-making model realistic"
  ]
}
```

2. ChatGPT (running AWM) will:
   - Load persona + planner + reviewer + executor prompts.
   - Generate a plan.
   - Review/correct the plan.
   - Execute the plan and return the final artifact.

## 2. Core update usage (`update_core`)

1. You provide an envelope like:

```jsonc
{
  "task_type": "update_core",
  "task": "Clarify how the planner should handle update_core tasks.",
  "context": null,
  "inputs": {
    "targets": ["awm/prompts/planner.md"]
  },
  "constraints": {},
  "output_format": "json",
  "success_criteria": [
    "Planner instructions clearly explain update_core behavior"
  ]
}
```

2. ChatGPT (running AWM) will:
   - Load the target files (or you paste them in the chat).
   - Plan how to modify them.
   - Produce updated file contents plus a git snippet.

3. You locally:
   - Pull the repo.
   - Apply the changes (copy/paste or by running the snippet).
   - Commit and push.

4. Next time AWM runs, it uses the new core files from this repo.
