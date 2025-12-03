# AWM v0.2.0 — ChatGPT-Native Autonomous Workflow Model

This repository contains the complete AWM v0.2.0 core designed to run *inside* ChatGPT.

- All prompts, schemas, and knowledge live in this repo.
- ChatGPT loads these files (read-only, via GitHub) and runs the AWM workflow.
- You apply any core changes locally with `git` and push them back here.

## High-level flow

1. You provide an AWM task "envelope" (a small JSON-like structure) in ChatGPT.
2. ChatGPT:
   - Loads the persona + planner + reviewer + executor prompts from this repo.
   - Runs planner → reviewer → executor as separate reasoning passes.
3. For normal tasks (`awm_task`):
   - You get the final artifact (doc, code, design, etc.).
4. For core updates (`update_core`):
   - ChatGPT proposes new file contents and a local git snippet.
   - You apply and push those changes.
   - Next run, ChatGPT uses the updated AWM core from this repo.

There is **no Python runtime** required for AWM itself. All execution happens inside ChatGPT.
Git and GitHub are only used to store and version the AWM core files.
