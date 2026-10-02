# Agent Arsenal (Cybersecurity Edition)

A curated collection of 1,150+ operational cybersecurity capabilities, red team playbooks, blue team detection workflows, and threat assessment skills for AI agents and LLMs.

---

## 19 Core Cyber Domains

The repository is structured across 19 specialized operational cybersecurity categories:

| Domain | Directory | Focus & Capabilities |
| :--- | :--- | :--- |
| **01** | `01-recon-osint` | Active/passive OSINT, subdomain discovery, certificate transparency, port surface mapping |
| **02** | `02-vulnerability-scanner` | Automated defect verification, surface scanning, CVE audit workflows |
| **03** | `03-exploit-development` | Benign PoCs, buffer boundary analysis, payload formatting, shellcode testing |
| **04** | `04-reverse-engineering` | Static & dynamic binary analysis, Ghidra, IDA Pro, Radare2, decompilation |
| **05** | `05-malware-analysis` | Artifact extraction, PE header triage, deobfuscation, sandbox behavioral analysis |
| **06** | `06-threat-hunting` | Hypothesis generation, MITRE ATT&CK mapping, Zeek analysis, LOLBINs hunting |
| **07** | `07-incident-response` | Breach containment, digital forensics, timeline reconstruction (Plaso/Hayabusa) |
| **08** | `08-network-security` | Packet capture analysis (Wireshark/Tshark), traffic baselining, firewall hardening |
| **09** | `09-web-security` | OWASP Top 10 auditing: SQLi, XSS, SSRF, IDOR, race conditions, CORS, JWT flaws |
| **10** | `10-cloud-security` | AWS, Azure, and GCP IAM misconfigurations, container escape, Kubernetes RBAC |
| **11** | `11-csoc-automation` | Alert triage optimization, SIEM correlation rules, SOAR playbooks |
| **12** | `12-log-analysis` | Windows Event Logs, Sysmon, Linux auditd, Athena queries, Splunk SPL |
| **13** | `13-crypto-analysis` | TLS/SSL cipher testing, post-quantum migration, algorithm confusion verification |
| **14** | `14-red-team-ops` | Active Directory exploitation (BloodHound, Kerberoasting, DCSync), C2 operations |
| **15** | `15-blue-team-defense` | AppLocker policies, CIS Benchmarks, endpoint detection (Wazuh, Falco), Zero Trust |
| **16** | `16-ai-llm-security` | Indirect prompt injection, model extraction, RAG poisoning, guardrail testing |
| **17** | `17-mobile-security` | Android/iOS static analysis (MobSF, Jadx), intent vulnerabilities, cert pinning |
| **18** | `18-ot-ics-security` | SCADA/ICS protocols (Modbus, DNP3, S7comm), Purdue model, IoT firmware audit |
| **19** | `19-grc-compliance` | ISO 27001, SOC 2 Type II, HIPAA, NIST CSF/RMF, CMMC Level 2, PCI-DSS audit prep |

In addition to the 19 numbered foundation directories, over 1,130 modular atomic skills (prefixed by `hunt-`, `analyzing-`, `detecting-`, `exploiting-`, `hardening-`, `testing-`, `triaging-`, `offensive-`, and `ctf-`) provide granular, task-specific execution playbooks.

---

## How to Import Skills into Your CLI & Models

### 1. Command Line Interfaces (CLIs)

#### A. Ollama CLI
Bake any cybersecurity skill directly into a custom local model via `Modelfile`:

```bash
# Clone the repository
git clone https://github.com/tejassroot/agent-arsenal.git
cd agent-arsenal

# Bake a skill into an Ollama model
cat << 'EOF' > Modelfile
FROM llama3.2
PARAMETER temperature 0.1
SYSTEM """
EOF

cat subdomain-enumeration/SKILL.md >> Modelfile
echo '"""' >> Modelfile

# Build and execute
ollama create sec-recon-agent -f Modelfile
ollama run sec-recon-agent "Enumerate subdomains for example.com"
```

#### B. `llm` CLI (Simon Willison's LLM)
Run skills on-demand or store them as reusable templates:

```bash
# Execute on-the-fly with skill context
llm -s "$(cat hunt-rce/SKILL.md)" "Audit this PHP controller snippet for command injection"

# Save as a permanent prompt template
cat subdomain-enumeration/SKILL.md | llm --system - --save sec-recon

# Run with any backend (local or cloud)
llm -t sec-recon "Scan target boundaries for internal endpoints"
llm -t sec-recon -m claude-3-5-sonnet "Assess cloud staging subdomains"
```

#### C. Claude Code CLI (`claude`)
Reference specific playbooks interactively during an engagement:

```bash
# Pass playbook as context to Claude Code
claude "Follow the playbook in ./wstg-web-pentest/SKILL.md to test authentication boundaries"

# Append directly to project instructions
cat 09-web-security/SKILL.md >> CLAUDE.md
```

#### D. Fabric CLI (`fabric`)
Export any skill to a native Fabric pattern:

```bash
mkdir -p ~/.config/fabric/patterns/threat-hunting
cat 06-threat-hunting/SKILL.md > ~/.config/fabric/patterns/threat-hunting/system.md
fabric -p threat-hunting < /var/log/syslog
```

#### E. Mods CLI (`mods`)
Pipe logs or network outputs through specialized skills:

```bash
# Analyze Zeek connection logs with detection skill
cat conn.log | mods "Using context in detecting-beaconing-patterns-with-zeek/SKILL.md, identify anomalies"
```

#### F. Shell-GPT (`sgpt`) & AIChat (`aichat`)
Create persistent command-line security roles:

```bash
# Shell-GPT role creation
sgpt --create-role sec-analyst < 07-incident-response/SKILL.md
sgpt --role sec-analyst "Analyze this suspicious base64 encoded PowerShell script"

# AIChat role definition
mkdir -p ~/.config/aichat/roles
cat << 'EOF' > ~/.config/aichat/roles/threat-hunter.md
---
model: openai:gpt-4o
---
EOF
cat 06-threat-hunting/SKILL.md >> ~/.config/aichat/roles/threat-hunter.md
aichat -r threat-hunter "Review these authentication logs"
```

---

### 2. Python & Direct API Usage (OpenAI / Anthropic / Gemini)

Dynamically load any skill file and pass it as a `system` instruction in your automation scripts:

```python
from pathlib import Path
from openai import OpenAI

client = OpenAI()

# Load the target skill
skill_path = Path("hunt-ssrf/SKILL.md")
skill_prompt = skill_path.read_text(encoding="utf-8")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "system",
            "content": f"You are an expert security auditor. Strictly follow this methodology:\n\n{skill_prompt}"
        },
        {
            "role": "user",
            "content": "Verify SSRF edge cases on a webhook URL parameter with internal redirect handling."
        }
    ]
)

print(response.choices[0].message.content)
```

---

### 3. Agent Frameworks (CrewAI, LangChain, AutoGen)

Dynamically register skills based on YAML frontmatter:

```python
import yaml
from pathlib import Path

def load_security_skill(skill_dir: str):
    skill_file = Path(skill_dir) / "SKILL.md"
    content = skill_file.read_text(encoding="utf-8")
    
    parts = content.split("---", 2)
    metadata = yaml.safe_load(parts[1]) if len(parts) >= 3 else {}
    instructions = parts[2].strip() if len(parts) >= 3 else content
    
    return {
        "name": metadata.get("name", Path(skill_dir).name),
        "description": metadata.get("description", ""),
        "category": metadata.get("category", "Cybersecurity"),
        "instructions": instructions
    }

skill = load_security_skill("hunt-sqli")
print(f"Loaded: [{skill['category']}] {skill['name']} - {skill['description']}")
```

---

### 4. Coding Assistants & Agent CLI Environments

For agent environments that discover skills from standard directories:

```bash
mkdir -p ~/.agent/skills/

# Copy desired security skills
cp -r 01-recon-osint ~/.agent/skills/
cp -r hunt-rce ~/.agent/skills/
cp -r wstg-web-pentest ~/.agent/skills/
```

---

## Searching and Finding Skills

Quickly search through the 1,150+ security skills by technique, tool, or CVE:

```bash
# Search by keyword in skill descriptions
grep -rn "description:.*Active Directory" --include="SKILL.md" .

# Search by MITRE ATT&CK ID
grep -rn "mitre_attack:.*T1003" --include="SKILL.md" .

# Search skills by vulnerability class
find . -maxdepth 1 -type d -name "*ssrf*"
```

---

## License & Operational Security Policy

All playbooks and verification procedures are strictly intended for authorized security audits, vulnerability triage, and defensive posture evaluation. When verifying defects, always adhere to non-destructive methodology and responsible disclosure standards.
