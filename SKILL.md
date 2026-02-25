---
name: skillguard
description: Security audit agent skills for vulnerabilities and malicious patterns. Use when asked to "scan a skill", "check if a skill is safe", "audit a skill for security", "review skill security", "is this skill malicious", or when installing/reviewing any third-party agent skill. Accepts local SKILL.md files, skill directories, .skill packages, or GitHub URLs. Maps findings to OWASP Agentic Top 10 (2026) and MITRE ATLAS.
---

# SkillGuard — Agent Skill Security Auditor

Audit agent skills against industry-standard frameworks before installation.

## How to Use

1. Read the target skill's SKILL.md and all files in the skill directory
2. Read `references/owasp-agentic-top10.md` for the risk categories
3. Read `references/mitre-atlas-mapping.md` for technique-level mapping
4. Read `references/audit-checklist.md` for the structured audit procedure
5. Perform the audit following the checklist
6. Output the report in the format below

## Audit Procedure

For each skill under review:

### Step 1 — Inventory
List all files in the skill. Note languages, dependencies, external URLs, and any scripts.

### Step 2 — Frontmatter Validation
Check SKILL.md has valid YAML frontmatter with `name` and `description` per the Agent Skills spec. Flag missing or malformed fields.

### Step 3 — Intent Verification
Read the `description` field. Then read ALL code and instructions in the skill. Answer: **does the skill's actual behavior match its stated purpose?** Mismatches are the strongest signal of malicious intent.

### Step 4 — Risk Assessment
Walk through each OWASP Agentic Top 10 category (see `references/owasp-agentic-top10.md`). For each category, check whether the skill exhibits relevant patterns. Map any findings to MITRE ATLAS techniques where applicable (see `references/mitre-atlas-mapping.md`).

### Step 5 — Verdict
Assign a verdict based on findings:

| Verdict | Criteria |
|---------|----------|
| ✅ **SAFE** | No findings, or only informational quality notes |
| ⚠️ **SUSPICIOUS** | Medium-severity findings that need human review |
| 🚨 **MALICIOUS** | High/critical findings indicating intentional harm or dangerous behavior |

## Report Format

```
══════════════════════════════════════════════
  SkillGuard Report: <skill-name>
══════════════════════════════════════════════
  Verdict:    ✅ SAFE / ⚠️ SUSPICIOUS / 🚨 MALICIOUS
  Files:      <count>
  Findings:   <count>
══════════════════════════════════════════════

Findings:

1. [SEVERITY] <title>
   OWASP: <Agentic Top 10 category>
   ATLAS: <MITRE ATLAS technique ID, if applicable>
   File: <path>:<line>
   Evidence: <snippet>
   Description: <explanation>

──────────────────────────────────────────────
Summary: <one-paragraph assessment>
Recommendation: <install / review manually / do not install>
══════════════════════════════════════════════
```

## Severity Levels

- 🔴 **CRITICAL** — Likely malicious; do not install
- 🟠 **HIGH** — Dangerous pattern requiring investigation
- 🟡 **MEDIUM** — Risky practice; review before installing
- 🔵 **LOW** — Quality/hygiene issue
- ⚪ **INFO** — Informational note
