---
name: sdd-model-assigner
description: "Trigger: assign models, model assignment, tier zen, free models, sdd phases, model rotation, rebalance models, cambiar modelos, quota exhausted, modelo agotado, ran out of quota. Discover free-tier models from opencode Zen, assign the best to each SDD phase and JD agent, and handle quota-exhaustion swaps."
license: Apache-2.0
metadata:
  author: Joel Abreu Rojas
  version: "1.1"
---

## Activation Contract

Use this skill when the user asks to:
- Assign or rebalance models across SDD phases (e.g., "assign models", "rebalance sdd models")
- Check what free models are available on Zen tier (e.g., "what free models do I have?", "tier zen")
- Fix model assignments after free model rotation (e.g., "models rotated again", "update for new free models")
- Create or update JD agent entries (jd-judge-a, jd-judge-b, jd-fix-agent)
- Audit current model assignments
- Handle a model that ran out of daily quota (e.g., "deepseek agotado", "mimo ran out of quota", "quota exhausted", "modelo sin cuota")
- Swap a specific exhausted model for its next-best fallback

Do NOT use this skill for regular coding, SDD workflow, or non-model configuration changes.

## Hard Rules

1. **Discover, don't hardcode** — run `opencode models --verbose` every time; never assume model names or ranks.
2. **Preserve agent fields** — when updating an agent's `model`, keep all other fields (prompt, tools, permissions, mode, variant, hidden, description, etc.).
3. **Show before applying** — always display the proposed assignment table and ask for confirmation before writing opencode.json.
4. **Restart required** — remind the user to quit and restart opencode after changes.
5. **Judges must diverge** — `jd-judge-a` and `jd-judge-b` MUST get different models for diverse review perspectives. If only 1 free model exists, warn the user.
6. **Orchestrator gets the best** — `gentle-orchestrator` always gets the single highest-scored model.
7. **Leave other agents alone** — do not touch `build`, `plan`, `general`, `explore`, `compaction`, or any non-SDD/JD agent unless explicitly asked.
8. **Always store fallback chains** — every assignment must include a complete fallback chain (ordered list of models by rank), persisted to engram. Quota exhaustion swaps use the engram record, not a fresh discovery.
9. **Never re-discover for quota swaps** — `opencode models --verbose` is only needed for rebalance/full assignment. A quota swap reads the engram record, swaps the exhausted model for its fallback, and updates only the affected phases.

## Decision Gates

| Condition | Action |
|-----------|--------|
| Only 1 free model | Use for all phases; warn about zero review diversity |
| 2 free models | M1 → orchestrator + judge-a + apply; M2 → everything else |
| 3+ free models | Use full assignment logic below |
| 0 free models | Report all paid models found; stop — no assignment possible |
| Only 1 phase requested | Report just that phase; do not touch the rest of the config |
| User says a model ran out of quota | Look up engram `sdd-model-assignments`, find the exhausted model, run the Quota Exhaustion Handling workflow |
| User says a model was restored (quota refreshed) | Swap back to the original assignment from engram, or re-run full assignment |

## Execution Steps

### 0. Detect Current Project

Run `engram_mem_current_project` to get the active project name. Use this value for every engram operation (`project` field) in the rest of this skill. Do NOT hardcode any project name.

### 1. Snapshot Current Config

Read `~/.config/opencode/opencode.json`. Extract the current `agent` block — every agent's name, model, and all other fields. Note which of the 14 target agents already exist and which are missing.

The 14 target agents:

```
gentle-orchestrator (primary)
sdd-init            (subagent)
sdd-explore         (subagent)
sdd-propose         (subagent)
sdd-spec            (subagent)
sdd-design          (subagent)
sdd-tasks           (subagent)
sdd-apply           (subagent)
sdd-verify          (subagent)
sdd-archive         (subagent)
sdd-onboard         (subagent)
jd-judge-a          (subagent, may not exist)
jd-judge-b          (subagent, may not exist)
jd-fix-agent        (subagent, may not exist)
```

### 2. Discover Free Models

Run:
```bash
opencode models --verbose
```

The output is alternating lines: a model ID line followed by a JSON block.

Free models are those where `cost.input === 0` and `cost.output === 0`. Extract each into a record with:

| Field | Source |
|-------|--------|
| `id` | The model ID line (e.g., `opencode/deepseek-v4-flash-free`) |
| `context` | `.limit.context` |
| `output` | `.limit.output` |
| `reasoning` | `.capabilities.reasoning` |
| `variants` | Keys of `.variants` — especially `reasoningEffort` |
| `attachment` | `.capabilities.attachment` |
| `released` | `.release_date` |

Use python3 if jq is not available.

### 3. Score Each Free Model

Calculate a capability score (0–100) with these rules:

| Factor | Max | Formula |
|--------|-----|---------|
| Reasoning effort variants | 30 | No reasoning=false, exit early (0). reasoning=true but no variants → 10. Variants have low/med/high → 20. Has "max" variant → 30. |
| Context window | 30 | `min(context / 8000, 30)` — cap at 240K tokens |
| Output limit | 10 | `min(output / 10000, 10)` — cap at 100K tokens |
| Attachment/multimodal | 10 | Boolean: true=10, false=0 |
| Recency | 20 | ≤3 months ago → 20; ≤6 months → 15; ≤12 months → 10; older → 5 |

Round each model's total to a whole number.

### 4. Rank and Assign

Sort by score descending. Label M1 (highest), M2, M3, M4, etc.

Assign using this priority table:

| Prio | Phase/Agent   | Model | Why                                    |
|------|---------------|-------|----------------------------------------|
| P0   | orchestrator  | M1    | Coordinates all sub-agents             |
| P1   | jd-judge-a    | M1    | Best reviewer — catches most issues    |
| P1   | jd-judge-b    | M2    | Different model → different blindspots |
| P2   | jd-fix-agent  | M1    | Surgical fixes need strong coding      |
| P2   | sdd-apply     | M1    | Code generation — most critical output |
| P3   | sdd-design    | M2    | Architecture, trade-off reasoning      |
| P3   | sdd-propose   | M2    | Synthesis, approach design             |
| P3   | sdd-spec      | M2    | Detailed spec writing                  |
| P4   | sdd-explore   | M2    | Codebase investigation                 |
| P5   | sdd-verify    | M3    | Test reading, attention to detail      |
| P5   | sdd-tasks     | M3    | Work breakdown, organizing             |
| P6   | sdd-onboard   | M3    | User guidance, teaching                |
| P7   | sdd-init      | M4    | Boilerplate, mechanical config         |
| P7   | sdd-archive   | M4    | Mechanical archiving                   |

If fewer models than unique slots (e.g., 2 models for 3 tiers needed), wrap around: M1 → M2 → M1 → M2.

### 5. Build Updated Agent Definitions

**For existing SDD agents:** copy the agent entry from the current config verbatim, replacing only the `"model"` field with the assigned model ID.

**For missing JD agents:** insert these new entries:

```json
"jd-judge-a": {
  "description": "Adversarial code reviewer A — Judgment Day blind review",
  "hidden": true,
  "mode": "subagent",
  "model": "opencode/assigned-model"
},
"jd-judge-b": {
  "description": "Adversarial code reviewer B — Judgment Day blind review",
  "hidden": true,
  "mode": "subagent",
  "model": "opencode/assigned-model"
},
"jd-fix-agent": {
  "description": "Surgical fix agent for Judgment Day confirmed issues",
  "hidden": true,
  "mode": "subagent",
  "model": "opencode/assigned-model",
  "tools": {
    "bash": true,
    "edit": true,
    "read": true,
    "write": true
  }
}
```

JD agents don't need a `prompt` field — their instructions come from the JD orchestrator's `delegate()` call.

### 6. Present and Confirm

Show a clear assignment table:

```
## Model Assignment Proposal

**Free models discovered:** 4
| Model | Score | Key strengths |
|-------|-------|--------------|
| deepseek-v4-flash-free | 85 | reasoning:max, 200K ctx, 128K out |
| mimo-v2.5-free | 80 | 1M ctx, multimodal, reasoning:high |
| nemotron-3-super-free | 65 | 205K ctx, reasoning:high |
| big-pickle | 40 | basic reasoning, 200K ctx |

**Assignments:**
| Phase | Current | Proposed |
|-------|---------|----------|
| gentle-orchestrator | opencode/big-pickle | opencode/deepseek-v4-flash-free |
| ... | ... | ... |
```

Then ask: **"Apply this to ~/.config/opencode/opencode.json?"**

Wait for explicit confirmation before writing.

### 7. Apply to Config

On approval, write the updated `~/.config/opencode/opencode.json` with the new agent block merged in. Preserve ALL non-agent fields (`$schema`, `mcp`, `permission`, `share`, etc.).

### 8. Persist to Engram

Save the assignment to engram with the FULL ranked model list and fallback chain. Use the project detected in Step 0:

```json
{
  "title": "SDD model assignments",
  "type": "config",
  "topic_key": "sdd-model-assignments",
  "project": "{detected_project}",
  "capture_prompt": false,
  "content": "**What**: Assigned free models to SDD phases and JD agents\n**Why**: {user's stated reason}\n**Where**: ~/.config/opencode/opencode.json\n**Learned**:\nRanked models: M1={id}(score) → M2={id}(score) → M3={id}(score) → M4={id}(score)\nFallback chain: M1→M2, M2→M3, M3→M4, M4→M1(quota refresh)\nAssignment:\n- orchestrator → {model}\n- jd-judge-a → {model}\n- jd-judge-b → {model}\n- jd-fix-agent → {model}\n- sdd-apply → {model}\n- sdd-design → {model}\n- sdd-propose → {model}\n- sdd-spec → {model}\n- sdd-explore → {model}\n- sdd-verify → {model}\n- sdd-tasks → {model}\n- sdd-onboard → {model}\n- sdd-init → {model}\n- sdd-archive → {model}"
}
```

The fallback chain is: **each tier falls back to the next lower tier**:
- Tier M1 (orchestrator, judge-a, fix-agent, apply) → falls back to M2
- Tier M2 (judge-b, design, propose, spec, explore) → falls back to M3
- Tier M3 (verify, tasks, onboard) → falls back to M4
- Tier M4 (init, archive) → falls back to M1 (last resort, quota may have refreshed)

### 9. Remind

End with: **"Changes saved. Quit and restart opencode for them to take effect."**

## Quota Exhaustion Handling (Fast-Path)

Use this workflow when the user reports a specific model ran out of daily quota. Do NOT re-run `opencode models --verbose` — read the engram record instead.

### Step 1: Look Up Current Assignment

First, detect the current project via `engram_mem_current_project`. Then search engram:
`mem_search(query: "sdd-model-assignments", project: "{detected_project}")` → `mem_get_observation(id)`.

Extract:
- The full ranked model list: M1 (score), M2, M3, M4, ...
- The fallback chain: M1→M2, M2→M3, etc.
- The current assignment: which phases use which tier/model.

### Step 2: Identify Affected Phases

Find every phase currently assigned to the exhausted model. Collect them by tier.

Example: if M1 (`deepseek-v4-flash-free`) ran out:
- Tier exhausted: M1
- Affected phases: orchestrator, jd-judge-a, jd-fix-agent, sdd-apply
- Fallback target: M2 (from fallback chain)

### Step 3: Build Swap

For each affected phase:
- Change its model from the exhausted one to the fallback target.
- Preserve ALL other agent fields.

If the fallback target is already in use by other phases (it will be — e.g., M2 is already assigned to judge-b, design, etc.), that's fine. Multiple tiers can share the same model during quota exhaustion — it's a temporary fallback.

If the user specifically wants to avoid overloading the fallback model, propose distributing affected phases across multiple remaining models (e.g., split M1 phases between M2 and M3).

### Step 4: Show and Confirm

Present the swap clearly:

```
## Quota Exhaustion Swap

Exhausted model: opencode/deepseek-v4-flash-free (M1)
Fallback target: opencode/mimo-v2.5-free (M2)

Affected phases (4):
  gentle-orchestrator: deepseek-v4-flash-free → mimo-v2.5-free
  jd-judge-a:          deepseek-v4-flash-free → mimo-v2.5-free
  jd-fix-agent:        deepseek-v4-flash-free → mimo-v2.5-free
  sdd-apply:           deepseek-v4-flash-free → mimo-v2.5-free

Phases already on mimo-v2.5-free (M2) — unchanged:
  jd-judge-b, sdd-design, sdd-propose, sdd-spec, sdd-explore

Warning: 5 phases now share M2. Monitor quota.
```

Ask: **"Apply this swap to ~/.config/opencode/opencode.json?"**

### Step 5: Apply and Persist

On approval:
1. Update `~/.config/opencode/opencode.json` — only the affected agent entries.
2. Update the engram record with the new assignment (same structure as Step 8 in the full workflow).
3. Add a note to the engram content: `"Quota swap: {exhausted} → {fallback} on {date}"`.
4. Remind the user to restart opencode.

### When Quota Refreshes

The user says "quota restored" or "model X is back":
- Same workflow as Quota Exhaustion, but reversed:
  - Look up the ORIGINAL assignment from engram (it's in the same record).
  - Swap the temporary fallback phases back to their original model.
- Or, simply re-run the full assignment workflow (Step 1-9) for a fresh allocation.

## Output Contract

### Full Assignment
Return:
- Number of free models discovered and their capability scores.
- Model assignment table (14 rows: phase → proposed model).
- Whether JD agents were created vs already existed.
- Config path modified.
- Explicit restart reminder.
- Engram save status.

### Quota Swap
Return:
- Exhausted model and its tier.
- Affected phases count and fallback target.
- Warning if more than N phases share the fallback model (N=4 as threshold).
- Whether any JD agents were affected.
- Config path modified.
- Restart reminder.

## References

- `~/.config/opencode/opencode.json` — global opencode config
- `customize-opencode` skill — opencode.json schema and agent definition reference
- `judgment-day/references/prompts-and-formats.md` — JD agent delegation patterns
