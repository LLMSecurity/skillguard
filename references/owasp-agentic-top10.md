# OWASP Top 10 for Agentic Applications (2026)

Reference for auditing agent skills against the OWASP Agentic Top 10.

For each category: what it is, what to look for in a skill, and example red flags.

---

## AG01 — Prompt Injection

**Risk:** Attacker manipulates agent behavior through crafted inputs embedded in skill instructions.

**What to look for in skills:**
- Hidden instructions in HTML comments, markdown comments, or invisible unicode
- Phrases like "ignore previous instructions", "do not mention this to the user", "you are now"
- Role manipulation: overriding agent identity, permissions, or safety guidelines
- Zero-width characters (U+200B–U+200F, U+2060–U+206F, U+FEFF) hiding text
- Base64/hex encoded instruction blocks decoded at runtime

---

## AG02 — Insecure Tool Utilization

**Risk:** Agent invokes tools/APIs in unintended or unsafe ways due to skill instructions.

**What to look for in skills:**
- Shell commands constructed from user input without sanitization (`subprocess.run(f"...")`)
- `eval()`, `exec()`, `Function()` on dynamic content
- Instructions to run arbitrary code from external sources
- Curl-pipe-bash patterns (`curl ... | sh`)
- Skills instructing the agent to bypass tool confirmation prompts

---

## AG03 — Excessive Agency / Privilege Escalation

**Risk:** Skill requests or acquires capabilities beyond its stated purpose.

**What to look for in skills:**
- Accessing files outside the skill's domain (e.g., `~/.ssh`, `~/.aws`, `.env`)
- Writing to shell configs (`~/.bashrc`, `~/.zshrc`), system dirs, or other skills
- Requesting `sudo` or elevated permissions
- Spawning background processes or daemons (`nohup`, `setsid`, `start_new_session`)
- `allowed-tools` in frontmatter that don't match the stated functionality
- Instructions to disable safety features or confirmation prompts

---

## AG04 — Insecure Output Handling

**Risk:** Skill produces outputs that could be interpreted as executable code or commands downstream.

**What to look for in skills:**
- Generating shell commands intended to be copy-pasted or auto-executed
- Producing code that interacts with sensitive resources without user confirmation
- Outputs containing embedded instructions for other agents in a multi-agent setup

---

## AG05 — Data Exfiltration / Sensitive Information Disclosure

**Risk:** Skill leaks user data, credentials, or sensitive information to external parties.

**What to look for in skills:**
- Outbound HTTP requests (`fetch`, `requests.post`, `curl --data`, `axios.post`) to undocumented domains
- Reading environment variables for API keys, tokens, secrets beyond stated need
- Accessing sensitive files: `~/.ssh/*`, `~/.aws/*`, `~/.config/*`, `.env`, `credentials.json`
- DNS exfiltration: encoding data in DNS queries (`dig`, `nslookup` with command substitution)
- Constructing URLs with sensitive data in query parameters or path segments
- Clipboard or screenshot capture without stated purpose

---

## AG06 — Supply Chain Vulnerabilities

**Risk:** Skill depends on compromised or unvetted external components.

**What to look for in skills:**
- Unpinned dependencies (`pip install requests` without `==version`)
- Package names suspiciously similar to popular packages (typosquatting)
- Remote code loading at runtime (`eval(fetch(...))`, `exec(requests.get(...).text)`)
- Post-install scripts that execute with elevated context
- Dependencies on unmaintained or very low-download packages
- `npm install` without lockfile

---

## AG07 — Insufficient Monitoring & Logging

**Risk:** Skill suppresses or avoids logging, making malicious actions harder to detect.

**What to look for in skills:**
- Instructions to disable logging or suppress output
- Redirecting stderr/stdout to /dev/null
- Using `--quiet` or `--silent` flags on operations that should be auditable
- Deleting history or log files

---

## AG08 — Agent Memory Poisoning

**Risk:** Skill manipulates the agent's persistent memory or context to influence future behavior.

**What to look for in skills:**
- Writing to MEMORY.md, AGENTS.md, SOUL.md, or other agent config files
- Instructions to "remember" specific behaviors or override future decision-making
- Modifying other skills' files
- Injecting context that persists beyond the skill's execution scope

---

## AG09 — Multi-Agent Exploitation

**Risk:** In multi-agent systems, a malicious skill in one agent compromises others.

**What to look for in skills:**
- Instructions targeting other agents (e.g., "tell the orchestrator to...")
- Cross-agent prompt injection via shared context or message passing
- Skills that attempt to spawn or communicate with other agent sessions

---

## AG10 — Denial of Service / Resource Abuse

**Risk:** Skill consumes excessive resources or disrupts agent availability.

**What to look for in skills:**
- Infinite loops or unbounded recursion
- Instructions to process extremely large files without limits
- Fork bombs or excessive process spawning
- Deliberate token-wasting patterns (e.g., asking the agent to repeat long texts)
- Crypto mining or other compute-intensive operations unrelated to stated purpose
