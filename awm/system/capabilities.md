# AWM v0.2.0 Capabilities

- Runs entirely inside ChatGPT as a set of prompts and schemas.
- Uses a Planner → Reviewer → Executor chain for most tasks.
- Supports two task types:
  - `awm_task`: normal work (docs, designs, code, analysis, etc.).
  - `update_core`: modify AWM core files in a controlled way.
- Reads all core files from this repository (via GitHub).
- Proposes updated core files plus local git snippets for changes.
- Leaves actual git operations (commit, push) to the user.
