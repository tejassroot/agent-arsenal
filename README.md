# Agent Arsenal

A comprehensive collection of 2,800+ agentic capabilities, security assessment playbooks, and service automation skills for AI agents and LLMs.

---

## Overview

This repository provides modular, production-ready skills with structured YAML frontmatter, execution workflows, automated playbooks, and tool schemas spanning:

* **Security & Vulnerability Assessment**: Reconnaissance, network scanning, cloud security, web penetration testing, and incident response.
* **Service Integrations & API Automations**: Cloud providers, SaaS platforms, databases, and developer tooling.
* **Agentic Workflows**: Multi-step reasoning templates, verification patterns, and operational execution scripts.

Each skill folder contains:
* `SKILL.md`: Metadata (`name`, `description`), operational guidelines, prerequisites, and step-by-step instructions.
* Supporting scripts, reference materials, or automation schemas where applicable.

---

## How to Import Skills into Your CLI & Models

### 1. Command Line Interfaces (CLIs)

#### A. Ollama CLI
Bake any skill directly into a custom Ollama model via `Modelfile`:

```bash
# Generate Modelfile with your chosen skill
cat << 'EOF' > Modelfile
FROM llama3.2
PARAMETER temperature 0.2
SYSTEM """
EOF

cat subdomain-enumeration/SKILL.md >> Modelfile
echo '"""' >> Modelfile

# Build and run your custom agent
ollama create sec-recon-agent -f Modelfile
ollama run sec-recon-agent "Enumerate subdomains for example.com"
```

#### B. `llm` CLI (Simon Willison's LLM)
Use any skill as an immediate system prompt or save it as a permanent reusable template:

```bash
# Run ad-hoc with skill as system prompt
llm -s "$(cat subdomain-enumeration/SKILL.md)" "Enumerate subdomains for target.com"

# Save as a permanent reusable prompt template
cat subdomain-enumeration/SKILL.md | llm --system - --save sec-recon

# Run with any provider (OpenAI, Anthropic, local Ollama models)
llm -t sec-recon "Run reconnaissance on staging.example.com"
llm -t sec-recon -m claude-3-5-sonnet "Audit cloud boundaries"
```

#### C. Claude Code CLI (`claude`)
Instruct Claude Code to load and follow any skill playbook during an interactive session or add it to project instructions:

```bash
# Run interactively referencing the skill
claude "Follow the playbook in ./subdomain-enumeration/SKILL.md and run recon on target.com"

# Or append skill directly to project instructions (CLAUDE.md)
cat subdomain-enumeration/SKILL.md >> CLAUDE.md
```

#### D. Fabric CLI (`fabric`)
Export any skill into a native Fabric pattern:

```bash
# Create custom pattern folder
mkdir -p ~/.config/fabric/patterns/subdomain-recon

# Copy the skill into the pattern system prompt
cat subdomain-enumeration/SKILL.md > ~/.config/fabric/patterns/subdomain-recon/system.md

# Execute pattern
fabric -p subdomain-recon -u "https://example.com"
```

#### E. Mods CLI (`mods`)
Pipe skill context directly into `mods` using any LLM backend:

```bash
# Pipe skill file directly into mods prompt
cat subdomain-enumeration/SKILL.md | mods "Execute the reconnaissance steps on target.com"

# Using local Ollama backend via mods
cat web-enumeration/SKILL.md | mods --model ollama/llama3.2 "Audit these endpoints"
```

#### F. Shell-GPT (`sgpt`) & AIChat (`aichat`)
Create persistent roles for fast terminal execution:

```bash
# Shell-GPT: create a custom role
sgpt --create-role sec-recon < subdomain-enumeration/SKILL.md
sgpt --role sec-recon "Perform subdomain discovery on example.com"

# AIChat: save as a role definition
mkdir -p ~/.config/aichat/roles
cat << 'EOF' > ~/.config/aichat/roles/recon.md
---
model: openai:gpt-4o
---
EOF
cat subdomain-enumeration/SKILL.md >> ~/.config/aichat/roles/recon.md
aichat -r recon "Run assessment on target.com"
```

---

### 2. Python & Direct API Usage (OpenAI / Anthropic / Gemini)

Dynamically load any skill and pass it as a `system` instruction in your code:

```python
from pathlib import Path
from openai import OpenAI

client = OpenAI()

# Load the desired skill
skill_path = Path("subdomain-enumeration/SKILL.md")
skill_prompt = skill_path.read_text(encoding="utf-8")

# Query the model with the skill active
response = client.chat.completions.create(
    model="gpt-4o",  # or claude-3-5-sonnet, gemini-2.0-flash, etc.
    messages=[
        {
            "role": "system",
            "content": f"You are an expert autonomous agent. You must strictly follow this operational skill:\n\n{skill_prompt}"
        },
        {
            "role": "user",
            "content": "Perform initial reconnaissance on example.com"
        }
    ]
)

print(response.choices[0].message.content)
```

---

### 3. Agent Frameworks (LangChain / CrewAI / AutoGen)

Parse YAML metadata (`name`, `description`) to dynamically route tasks to appropriate skills:

```python
import yaml
from pathlib import Path

def load_skill(skill_dir: str):
    skill_file = Path(skill_dir) / "SKILL.md"
    content = skill_file.read_text(encoding="utf-8")
    
    parts = content.split("---", 2)
    metadata = yaml.safe_load(parts[1])
    instructions = parts[2].strip()
    
    return {
        "name": metadata.get("name"),
        "description": metadata.get("description"),
        "instructions": instructions
    }

# Example: register as a CrewAI or LangChain agent prompt/tool
skill = load_skill("agent-arsenal/web-enumeration")
print(f"Loaded: {skill['name']} - {skill['description']}")
```

---

### 4. IDEs & Coding Agents (Cursor, Antigravity, Open WebUI)

Copy selected skills to your agent's local skills directory for automatic discovery:

```bash
# For agents supporting local skills directories:
mkdir -p ~/.agent/skills/

# Copy one or multiple skills:
cp -r agent-arsenal/subdomain-enumeration ~/.agent/skills/
cp -r agent-arsenal/hunt-rce ~/.agent/skills/
```

In chat interfaces like **Open WebUI**, **ChatGPT Custom GPTs**, or **Claude Projects**, upload or paste the `SKILL.md` directly into the system instructions or project knowledge files.

---

## Searching and Finding Skills

With over 2,800 skills available, you can quickly locate skills using CLI tools:

```bash
# Search by keyword in skill descriptions
grep -rn "description:.*docker" --include="SKILL.md" .

# Search skills by category in YAML frontmatter
grep -rn "category:.*Cloud Security" --include="SKILL.md" .

# Find skill folders matching a keyword
find . -maxdepth 1 -type d -name "*recon*"
```

---

## License & Usage Policy

This repository is maintained for authorized security testing, code auditing, research, and infrastructure automation. Follow all applicable laws and terms of service when testing targets.
