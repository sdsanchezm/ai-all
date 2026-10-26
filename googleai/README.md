# Google AI documentation

To bring Google AI agents, project-folder context, and modular **Agent Skills** directly into your workspace, you can configure your repository using the open **Agent Skills standard** (supported by Google's development platforms and developer tooling).

An agent skill is a structured directory layout that teaches an AI coding agent your custom workflows, preferred SDK versions, and architecture rules without clogging its context window.

---

### Step 1: Initialize the Skills Directory in Your Project

To give agents access to custom skills inside your project folder, create a `.skills/` (or `.agents/skills/`) directory at the root of your project repository.

Each skill lives in its own sub-folder containing a mandatory `SKILL.md` file and optional execution scripts:

```text
your-project-folder/
├── .skills/
│   ├── database-migration/
│   │   ├── SKILL.md
│   │   └── scripts/
│   │       └── validate_schema.py
│   └── api-guidelines/
│       └── SKILL.md
├── src/
└── package.json

```

---

### Step 2: Write a `SKILL.md` File

The agent reads the YAML frontmatter at startup to understand what the skill does, and loads the Markdown body instructions dynamically only when that skill is triggered.

Create a file at `.skills/api-guidelines/SKILL.md`:

```markdown
---
name: api-guidelines
description: Enforces strict REST endpoint naming conventions, error response shapes, and input validation schemas for this project.
---

# API Development Guidelines

When creating or modifying routes in `src/api/`:
1. **Response Envelope:** All JSON responses must return `{ "success": boolean, "data": any, "error": string | null }`.
2. **Validation:** Use Zod for all incoming request body validations before passing data to services.
3. **Error Codes:** Return standard HTTP status codes (400 for bad input, 401 for unauthorized, 404 for missing resources, 500 for unhandled exceptions).

```

---

### Step 3: Connect Pre-Built Google & Open-Source Skill Packs

You don't have to write every skill from scratch. You can pull production-grade engineering skills or official Google Cloud/SDK skill packs directly into your project folder using community/CLI registries (such as the open `skills` CLI):

```bash
# Install the skills CLI or add standard skill packages to your repository
npx skills add google/skills

```

This automatically drops specialized, agent-ready workflows (such as secure coding guidelines, cloud deployment checks, or framework best practices) right into your folder structure so your coding agent can reference them locally.

---

### Step 4: Hook Up Your Agent Workspace

Once your folder structure and skills are in place, connect your project to your AI development environment (such as **Gemini Code Assist**, or agentic developer environments):

1. **Open your project folder** in your IDE (like VS Code or JetBrains with Gemini Code Assist enabled).
2. **Index the workspace:** Ensure your AI extension has read access to your workspace files so it can index your codebase alongside your new `.skills/` configurations.
3. **Prompt your agent:** Invoke your agent directly in your editor's chat panel or terminal interface with context-aware prompts:
> *"Using the rules defined in our `.skills/api-guidelines` skill, generate a new user profile endpoint in `src/api/`."*


The agent will selectively read the relevant skill instructions, follow your project's deterministic rules, execute any bundled helper scripts if necessary, and build code tailored specifically to your repository.

To empower an AI agent to run tests or validate code automatically within your project, you can bundle executable helper scripts directly inside a skill directory. This allows the agent to execute specific commands or scripts in your repository without needing hardcoded instructions in every prompt.

Here is a complete example of how to structure a `.skills/` folder containing an automated testing skill and a helper script.

---

### 1. Folder Directory Structure

```text
your-project-folder/
├── .skills/
│   └── test-runner/
│       ├── SKILL.md                      # Instructions and metadata for the agent
│       └── scripts/
│           └── run_smoke_tests.py        # The executable helper script
├── src/
└── package.json

```

---

### 2. The `SKILL.md` File

The `SKILL.md` file tells the AI agent *when* and *how* to use the helper script. It acts as the prompt guide for that specific capability.

Create `.skills/test-runner/SKILL.md`:

```markdown
---
name: test-runner
description: Executes local smoke tests and validates database connectivity before code review.
---

# Test Runner Skill

When you finish writing or modifying code in `src/`, you must verify the changes using the project's local test script.

## Instructions
1. Open a terminal session in the workspace.
2. Execute the bundled validation script:
   ```bash
   python .skills/test-runner/scripts/run_smoke_tests.py

```

3. If the script returns a non-zero exit code or failure messages, analyze the error logs, fix the code, and re-run the script until all checks pass.

```

---

### 3. The Helper Script (`run_smoke_tests.py`)

The helper script contains the actual deterministic logic. Because it lives right in your repository, the AI agent can execute it using its terminal tool to verify its own work.

Create `.skills/test-runner/scripts/run_smoke_tests.py`:

```python
#!/usr/bin/env python3
import sys
import os

def check_environment():
    print("-> Checking environment variables...")
    if not os.path.exists("package.json"):
        print("❌ Error: package.json not found in root directory.")
        return False
    print("✅ Environment looks good.")
    return True

def run_mock_checks():
    print("-> Running local smoke tests...")
    # Add your specific test assertions or checks here
    tests_passed = True
    
    if tests_passed:
        print("✅ All smoke tests passed successfully!")
        return True
    else:
        print("❌ Smoke tests failed.")
        return false

if __name__ == "__main__":
    env_ok = check_environment()
    tests_ok = run_mock_checks()
    
    if env_ok and tests_ok:
        sys.exit(0)
    else:
        sys.exit(1)

```

---

### How the Agent Uses It

When you prompt your AI coding agent (e.g., *"Implement the user login route and run our tests"*), the agent will:

1. Detect that the `.skills/test-runner/SKILL.md` instruction file is relevant to the task.
2. Read the instructions to find the script path.
3. Automatically execute `python .skills/test-runner/scripts/run_smoke_tests.py` in its terminal environment.
4. Read the script's output (`✅ All smoke tests passed successfully!` or error logs) and self-correct if anything fails before presenting the final code to you.

# how to use Google AI enterprise

To code using Google AI in an enterprise setting, your organization will use **Gemini Code Assist Enterprise**. This service integrates enterprise-grade security and your company's private codebase context directly into your development workflow.

To connect your enterprise license and start coding inside your project folders, follow these setup and execution steps:

---

### Step 1: Ensure Enterprise Access & API Enablement

Before connecting your editor, your Google Cloud administrator needs to ensure your account has the correct permissions:

1. Your admin must enable the **Gemini for Google Cloud API** (`cloudaicompanion.googleapis.com`) on your enterprise's Google Cloud project.
2. You must be assigned the **Gemini for Google Cloud User** and **Service Usage Consumer** IAM roles in the Google Cloud Console.

---

### Step 2: Install the Gemini Code Assist Plugin in Your IDE

Gemini Code Assist plugs directly into the most popular development environments (such as **VS Code**, **JetBrains IDEs** like IntelliJ, PyCharm, GoLand, and Google Cloud Workstations).

1. Open your IDE (e.g., VS Code).
2. Go to the **Extensions/Plugins Marketplace** and search for **Gemini Code Assist**.
3. Click **Install** and restart your IDE if prompted.

---

### Step 3: Link Your Enterprise Google Cloud Project

Once installed, authenticate your enterprise credentials to connect your project repository to your company's secure cloud environment:

1. Click the **Gemini Code Assist** icon in your IDE activity bar.
2. Click **Login to Google** and authenticate using your enterprise Google account.
3. Select your company's **Google Cloud Project** that has the Gemini Code Assist Enterprise subscription enabled.

---

### Step 4: Leverage Local Context and Enterprise Customizations

With Gemini Code Assist Enterprise active, you can build inside your project folder using advanced enterprise features:

* **Local Codebase Awareness:** Gemini automatically indexes your open project folder locally, ensuring code completions, whole-function generations, and chat answers align with your project structure.
* **Private Codebase Customization:** Your enterprise admins can connect your company's private repositories or internal documentation libraries so Gemini references your proprietary libraries, design systems, and internal APIs.
* **Agentic Workflows:** Use the chat panel or terminal integrations to prompt agents to perform multi-file edits, handle debugging tasks, or generate comprehensive test suites tailored to your workspace rules.
