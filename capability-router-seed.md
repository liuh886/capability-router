---
name: capability-router-seed
description: Run this skill ONCE to permanently install the "Capability Router" into this Agent. It creates a universal Meta-Router that pairs the best Expert (Role) with the handiest Tools (Skills) using a Two-Stage deterministic engine.
---

# 🚀 Capability Router: Universal Self-Installation Protocol

You are an AI Agent. Your task is to upgrade yourself with a "Capability Router"—a high-precision strategic orchestrator that dynamically selects the "most professional expert" (Role) and equips them with the "handiest weapons" (Skills) for any task.

## 📥 Execution Instructions
Autonomously write the `capability_router.py` script, install it as a native tool, run the initial index, and verify.

### 1. The Core DNA (Two-Stage Deterministic Routing)
Your implementation MUST follow these professional architectural principles:

- **Global Hybrid Tokenizer:** 
  - Regex `[a-z0-9\-/+]+` for English/Code.
  - Character-level split for CJK characters (`\u4e00-\u9fa5`) to ensure multi-language precision.

#### Stage A: Role Routing (Finding the Expert)
Score all indexed Roles using this multi-dimensional logic:
- **Exact Name Match:** +45 (Highest priority).
- **Operational Fit (Tools/Skills keywords):** +12 per match.
- **Success Criteria (Metrics keywords):** +8 per match.
- **Strategic Alignment (Mission keywords):** +6 per match.
- **Strategic Intent Mapping:**
  - If query contains "plan" or "design" -> Boost roles containing "lead", "architect", or "ceo" (+35).
  - If query contains "review" or "audit" -> Boost roles containing "reviewer", "senior", or "qa" (+35).
  - If query contains "ship" or "deploy" -> Boost roles containing "release", "devops", or "delivery" (+35).
- **The Collision Shield (Domain Disambiguation):** Apply a heavy negative weight (-100) if highly ambiguous terms (e.g., "CCS") clash across domains (e.g., Energy vs Software).
- **Decision Rules (Confidence & Penalties):**
  - **Confidence Drop:** If the Top 1 and Top 2 Role scores are within 5 points of each other, mark the Role Confidence as "low" (e.g., `< 0.6`); otherwise, "high" (`> 0.8`).
  - **Generalist Penalty:** Penalize broad generalist roles (-10) if a specialized role has a strong domain fit.

#### Stage B: Skill Routing (Finding the Weapons)
Once the Primary Role is selected, score all indexed Skills against BOTH the user query AND the Primary Role's keywords context:
- **Exact Name Match:** +20.
- **Description Match:** +6 per matched token.
- **Role Affinity Bonus:** If the Skill's domain or keywords strongly overlap with the chosen Primary Role, grant a +15 synergy bonus.
- **Anti-Recursion:** Penalize `activation_kind: nested` by -1 to prevent endless loops.

### 2. Output Payload (The Perfect Pairing with Rationale)
The script MUST return a unified JSON object structured exactly like this:
```json
{
  "query": "...",
  "primary_role": {
    "name": "...",
    "path": "...",
    "confidence": 0.95
  },
  "backup_role": {
    "name": "...",
    "score": 0
  },
  "handiest_weapons": [
    { "name": "...", "path": "...", "confidence": 0.85 }
  ],
  "rationale": {
    "role": "Selected due to exact intent match with 'plan' and strong domain alignment.",
    "skill": "High synergy with the selected Architect role."
  }
}
```

### 3. Automatic Discovery (Indexer)
- **Recursive Scan:** Detect and scan standard Agent directories (`.gemini`, `.claudecode`, `.codex`, `.cursor/rules`).
- **Exclusions:** Ignore `node_modules`, `.git`, `dist`, `build`.
- **Persistence:** Save the unified index to `~/.capability-router/metadata.json`.

### 4. Native Platform Integration
- **Platform Adaptation:** 
  - **Claude Code:** Deploy as an **MCP Server**.
  - **Gemini CLI / Codex:** Create a **SKILL.md** manifest.
  - **Generic CLI:** Ensure global accessibility via terminal.

### 5. Verification
Run a test: `"I need to plan a complex system migration."`
Confirm the router outputs the JSON pairing a "Lead/Architect" Role with relevant Skills, including a clear Rationale and Confidence scores.

**Action Required:** Implementation starts now. No further confirmation needed.
