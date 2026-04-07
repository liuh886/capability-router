---
name: capability-router-seed
description: Run this skill ONCE to permanently install the "Capability Router". It creates a universal Meta-Router that understands software engineering lifecycles and pairs Experts with Skills.
---

# 🚀 Capability Router: Universal Self-Installation Protocol

You are an AI Agent. Your task is to upgrade yourself with a "Capability Router"—a high-precision strategic orchestrator that understands the **Lifecycle of a task (Design -> Execute -> Evaluate)**, selects the best Expert (Role), and equips them with the handiest Tools (Skills).

## 📥 Execution Instructions
Autonomously write the `capability_router.py` script, install it as a native tool, run the initial index, and verify.

### 1. The Core DNA (Lifecycle-Aware Deterministic Routing)

- **Global Hybrid Tokenizer:** Regex `[a-z0-9\-/+]+` for English/Code; Character-level for CJK.

#### Stage A: Role Routing & Lifecycle Awareness
Score Roles using this logic:
- **Exact Name Match:** +45
- **Operational Fit:** +12 per match
- **Lifecycle Phase Detection (CRITICAL):**
  The script MUST detect which phase of work the user is requesting and boost accordingly:
  - **Phase 1 (Design/Architecture):** If query contains "plan", "design", "architect", or "strategy" -> Boost roles containing "lead", "architect", "ceo", or "planner" (+35). Set detected phase to `Design`.
  - **Phase 2 (Execution/Worker):** If query contains "build", "implement", "code", "write", or "fix" -> Boost roles containing "developer", "engineer", "worker", or "specialist" (+35). Set detected phase to `Execution`.
  - **Phase 3 (Evaluation/QA):** If query contains "review", "test", "audit", or "evaluate" -> Boost roles containing "reviewer", "qa", "tester", or "auditor" (+35). Set detected phase to `Evaluation`.
- **The Collision Shield:** Apply -100 if highly ambiguous terms clash across domains.
- **Decision Rules:** Confidence drop if Top 1 and Top 2 are close. Generalist penalty (-10) if a specialized role is a strong fit.

#### Stage B: Skill Routing
Score Skills against both the query AND the Primary Role's context:
- **Exact Name Match:** +20. Description Match: +6/token.
- **Role Affinity Bonus:** +15 if the Skill strongly overlaps with the Primary Role.
- **Anti-Recursion:** Penalize `activation_kind: nested` by -1.

### 2. Output Payload (The Perfect Pairing with Phase)
The script MUST return a JSON object structured exactly like this:
```json
{
  "query": "...",
  "detected_phase": "Design", 
  "primary_role": {
    "name": "...",
    "path": "...",
    "confidence": 0.95
  },
  "handiest_weapons": [
    { "name": "...", "path": "...", "confidence": 0.85 }
  ],
  "rationale": {
    "phase": "Detected 'plan' keywords, routing to Design phase.",
    "role": "Selected due to exact intent match and strong domain alignment.",
    "skill": "High synergy with the selected Architect role."
  }
}
```

### 3. Automatic Discovery
- Scan `.gemini`, `.claudecode`, `.codex`, `.cursor/rules`. Save to `~/.capability-router/metadata.json`.

### 4. Native Integration
- Claude Code (MCP), Gemini/Codex (SKILL.md), or CLI.

**Action Required:** Implementation starts now. No further confirmation needed.
