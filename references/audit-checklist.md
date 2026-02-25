# SkillGuard Audit Checklist

Structured procedure for auditing an agent skill. Follow each section in order.

---

## 1. File Inventory

- [ ] List all files in the skill directory
- [ ] Note total file count and types (`.md`, `.py`, `.sh`, `.js`, etc.)
- [ ] Flag any binary files, symlinks, or files > 500KB
- [ ] Check for hidden files (dotfiles)

## 2. Frontmatter Check

- [ ] SKILL.md exists
- [ ] Valid YAML frontmatter with `---` delimiters
- [ ] `name` field present, lowercase alphanumeric + hyphens, ≤64 chars
- [ ] `description` field present, non-trivial (≥20 chars), ≤1024 chars
- [ ] Name matches parent directory name
- [ ] No consecutive hyphens in name

## 3. Intent Match Analysis

This is the most important check. Read the entire skill carefully.

- [ ] What does the `description` say the skill does?
- [ ] What does the skill ACTUALLY do? (read all files, not just SKILL.md)
- [ ] **Do they match?** Flag any undisclosed behaviors.
- [ ] Are there capabilities not mentioned in the description?
- [ ] Does the skill access resources unrelated to its stated purpose?

**Key principle:** A skill that accesses GitHub APIs to manage repos is fine if its description says "manage GitHub repos." The same access in a skill described as "format markdown files" is a red flag.

## 4. OWASP Agentic Top 10 Walkthrough

For each category, check applicable patterns:

### AG01 — Prompt Injection
- [ ] Hidden instructions (HTML/markdown comments, invisible unicode)
- [ ] Override phrases ("ignore previous", "do not tell the user", "you are now")
- [ ] Encoded instruction blocks (base64, hex, rot13)
- [ ] Zero-width or homoglyph characters

### AG02 — Insecure Tool Utilization
- [ ] Shell commands from dynamic input (`subprocess(f"...")`, `os.system(f"...")`)
- [ ] `eval()` / `exec()` / `Function()` on non-constant input
- [ ] `curl | sh` or equivalent remote code execution
- [ ] Instructions to bypass tool confirmation

### AG03 — Excessive Agency
- [ ] File access outside skill's domain (`~/.ssh`, `~/.aws`, `.env`, `/etc/`)
- [ ] Writes to shell configs or system directories
- [ ] `sudo` usage or privilege elevation
- [ ] Background process spawning (`nohup`, `&`, `setsid`, `daemon`)
- [ ] Disabling safety features or confirmations

### AG04 — Insecure Output
- [ ] Generating executable commands for auto-execution
- [ ] Cross-agent instruction injection
- [ ] Outputs that could be reinterpreted as prompts

### AG05 — Data Exfiltration
- [ ] Outbound HTTP to undocumented domains
- [ ] Environment variable harvesting (API keys, tokens, secrets)
- [ ] Reading sensitive credential files
- [ ] DNS exfiltration patterns
- [ ] Data in URL query parameters

### AG06 — Supply Chain
- [ ] Unpinned dependencies
- [ ] Typosquatting package names (Levenshtein distance ≤1 from popular packages)
- [ ] Runtime code fetching from external URLs
- [ ] Dependencies on unknown/unmaintained packages

### AG07 — Insufficient Monitoring
- [ ] Suppressing logs or output (`> /dev/null`, `--quiet`)
- [ ] Deleting history or audit trails
- [ ] Instructions to hide activity

### AG08 — Memory Poisoning
- [ ] Writes to agent memory files (MEMORY.md, AGENTS.md, SOUL.md)
- [ ] Modifying other skills
- [ ] Persistent context injection

### AG09 — Multi-Agent Exploitation
- [ ] Instructions targeting other agents
- [ ] Cross-agent prompt injection via shared context
- [ ] Spawning or messaging other agent sessions

### AG10 — Resource Abuse
- [ ] Unbounded loops or recursion
- [ ] Excessive process spawning
- [ ] Token-wasting patterns
- [ ] Compute-intensive operations unrelated to purpose

## 5. Obfuscation Detection

Not a separate OWASP category but a strong malicious signal:

- [ ] Base64-encoded executable payloads (`atob`, `b64decode`, `Buffer.from`)
- [ ] Character code construction (`String.fromCharCode`, `chr()` chains)
- [ ] Multi-layer encoding (nested decode calls)
- [ ] Intentionally misleading variable names
- [ ] Minified or obfuscated code in a skill context (unusual — skills should be human-readable)

## 6. Verdict Decision

| Condition | Verdict |
|-----------|---------|
| No findings or only INFO/LOW quality notes | ✅ SAFE |
| Intent mismatch OR any MEDIUM finding | ⚠️ SUSPICIOUS |
| Any HIGH/CRITICAL finding OR clear malicious intent | 🚨 MALICIOUS |

**When in doubt, err toward SUSPICIOUS.** It's better to flag for human review than to miss a threat.
