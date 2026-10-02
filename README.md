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

## How to Import Skills into Your Model

### 1. Ollama (via Modelfile)

You can package any skill directly into a custom Ollama model using a `Modelfile`.

```bash
# Clone the repository
git clone https://github.com/tejassroot/agent-arsenal.git
cd agent-arsenal

# Pick a base model (e.g. llama3.2, mistral, qwen2.5, deepseek-r1)
# and bake the chosen skill into its system instructions:
cat << 'EOF' > Modelfile
FROM llama3.2

# Set parameters
PARAMETER temperature 0.2

# Inject the chosen skill as system prompt
SYSTEM """
EOF

cat subdomain-enumeration/SKILL.md >> Modelfile
echo '"""' >> Modelfile

# Build and run your specialized agent
ollama create sec-recon-agent -f Modelfile
ollama run sec-recon-agent
```

---

### 2. Python / OpenAI / Anthropic / Gemini API

Load any skill dynamically and pass it into the `system` message of your model:

```python
from pathlib import Path
from openai import OpenAI

client = OpenAI()

# Load the desired skill
skill_path = Path("agent-arsenal/subdomain-enumeration/SKILL.md")
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

### 3. Agent Frameworks (LangChain, CrewAI, AutoGen)

Skills can be loaded dynamically based on their YAML frontmatter (`name` and `description`):

```python
import yaml
from pathlib import Path

def load_skill(skill_dir: str):
    skill_file = Path(skill_dir) / "SKILL.md"
    content = skill_file.read_text(encoding="utf-8")
    
    # Parse YAML frontmatter
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
print(f"Loaded skill: {skill['name']} - {skill['description']}")
```

---

### 4. AI Coding Assistants & CLI Agents (Cursor, Antigravity, Open WebUI)

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
