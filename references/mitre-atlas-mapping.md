# MITRE ATLAS Technique Mapping for Agent Skills

Maps OWASP Agentic Top 10 categories to specific MITRE ATLAS techniques.
Use this to provide precise technique IDs in audit findings.

---

## Reconnaissance

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0000 | Search for Victim's Publicly Available Research Materials | AG05 | Skill reads public profile info to target exfiltration |
| AML.T0001 | Search for Publicly Available Adversarial Vulnerabilities | AG01 | Skill references known prompt injection techniques |

## Initial Access

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0051 | LLM Prompt Injection: Direct | AG01 | Explicit override instructions in skill content |
| AML.T0051.001 | LLM Prompt Injection: Direct — Stored | AG01 | Malicious instructions persisted in skill files |
| AML.T0051.002 | LLM Prompt Injection: Indirect | AG01, AG08 | Instructions hidden in data the agent processes |
| AML.T0053 | Adversarial Search Engine Optimization | AG06 | Skill marketplace manipulation to promote malicious skill |

## Execution

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0040 | ML Model Inference API Access | AG02 | Skill abuses model API access for unauthorized purposes |
| AML.T0043 | LLM Plugin Compromise | AG02, AG06 | Compromised skill acting as a trojan plugin |
| AML.T0052 | Phishing | AG01, AG05 | Skill generates phishing content or harvests credentials |
| AML.T0054 | LLM Jailbreak | AG01 | Jailbreak payloads in skill instructions |

## Persistence

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0055 | LLM Prompt Injection: Persistent Memory Manipulation | AG08 | Writes to agent memory/config files |
| AML.T0020 | Poison Training Data | AG08 | Modifies data the agent learns from |

## Privilege Escalation

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0044 | Full LLM Access | AG03 | Skill attempts to gain unrestricted agent capabilities |
| AML.T0056 | LLM Excessive Agency Exploitation | AG03 | Skill leverages agent's broad permissions beyond stated scope |

## Exfiltration

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0024 | Exfiltration via ML Inference API | AG05 | Data encoded in API calls |
| AML.T0025 | Exfiltration via Cyber Means | AG05 | HTTP POST, DNS tunneling, or file upload to external server |
| AML.T0048 | Transfer via LLM Conversation | AG05, AG09 | Data leaked through agent responses or cross-agent messages |

## Impact

| ATLAS ID | Technique | Relevant OWASP | Skill Indicators |
|----------|-----------|----------------|-----------------|
| AML.T0029 | Denial of ML Service | AG10 | Resource exhaustion, infinite loops, token waste |
| AML.T0047 | ML Intellectual Property Theft | AG05 | Stealing model outputs, prompts, or proprietary data |
| AML.T0049 | LLM Output Manipulation | AG04 | Manipulating agent outputs for downstream exploitation |

---

## How to Use in Reports

When a finding maps to a known technique, include it as:

```
OWASP: AG01 — Prompt Injection
ATLAS: AML.T0051.001 (LLM Prompt Injection: Direct — Stored)
```

Not all findings will map to ATLAS techniques. Use ATLAS IDs when the technique mapping is clear; omit when findings are more general.
